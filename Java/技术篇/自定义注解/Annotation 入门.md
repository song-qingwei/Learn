[TOC]

### 自定义注解入门

------

要定义自己的注解，并使用注解，必须先要了解Java为我们提供的元注解和相关定义注解的语法。

#### 一、元注解

元注解的作用就是负责注解其他注解。Java 5.0 定义了4个标准的 meta-annotation 类型，它们用来提供对其它annotation 类型作说明。定义的元注解有：

1. `@Target`
2. `@Retention`
3. `@Documented`
4. `@Inherited`

这些类型和它们所支持的类在 `java.lang.annotation` 包中可以找到。

##### 1. @Target（目标）

`@Target` 说明了 Annotation 所修饰的对象范围。Annotation 可被用于 `packages`、`types（类、接口、枚举、Annotation类型）`、`类型成员（方法、构造方法、成员变量、枚举值）`、`方法参数和本地变量（如循环变量、catch参数）`。在 Annotation 类型的声明中使用了 `@Target` 可更加明晰修饰的目标。

**作用：** 用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

取值（`ElementType`）有：

1. `CONSTRUCTOR`：用于描述构造器
2. `FIELD`：用于描述域
3. `LOCAL_VARIABLE`：用于描述局部变量
4. `METHOD`：用于描述放大
5. `PACKAGE`：用于描述包
6. `PARAMETER`：用于描述参数
7. `TYPE`：用于描述类、接口或 `enum` 声明

##### 2. @Retention（保留）

`@Retention` 定义了该 Annotation 被保留的时间长短：某些 Annotation 仅出现在源代码中，而被编译器丢弃；而另一些却被编译在 class 文件中；编译在 class 文件中的 Annotation 可能会被虚拟机忽略，而另一些在 class 被装载时将被读取。使用这个 meta-annotation 可以对 Annotation 的<font color='#FF3399'>`生命周期`</font>限制。

**作用：** 表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）

取值（RetentionPolicy）有：

1. `SOURCE`：在源文件中有效（即源文件保留）
2. `CLASS`：在class文件中有效（即class保留）
3. `RUNTIME`：在运行时有效（即运行时保留）

`RetentionPolicy` 的属性值是 RUNTIME，这样注解处理器可以通过反射，获取到该注解的属性值，从而去做一些运行时的逻辑处理。

##### 3. @Documented（记录）

`@Documented` 用于描述其它类型的 annotation 应该被作为被标注的程序成员的公共 `API`，因此可以被例如 `javadoc` 此类的工具文档化。Documented 是一个标记注解，没有成员。

##### 4. @Inherited（遗传）

`@Inherited` 元注解是一个标记注解，@Inherited 阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited 修饰的annotation类型被用于一个 class，则这个 annotation 将被用于该 class 的子类。

#### 二、自定义注解

使用 `@interface`自定义注解时，自动继承了 `java.lang.annnotation` 接口，有编译程序自动完成其他细节。在定义注解时，不能继承其他注解或接口。@interface 用来声明一个注解，其中的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型（返回值类型只能是`基本类型`、`Class`、`String`、`enum`）。可以通过 default 来声明参数的默认值。

**定义注解格式：**

```java
public @interface 注解名 {定义体}
```

**注解元素的默认值**

注解元素必须有确定的值，要么在定义注解的默认值中指定，要么在使用注解时指定，飞基本类型的注解的值不可为 `null`。因此，使用`空字符串或0`作为默认值是一种常用的做法。

如果没有用来读取注解的方法和工作，那么注解也就不会比注释更有用处了。

#### 三、使用注解

以拦截器拦截注解为例：

```java
import com.cn.common.constants.MallWebConstant;
import com.cn.common.utils.CookieUtil;
import com.cn.common.annotation.Anonymous;
import com.cn.sso.controller.base.BaseController;
import com.cn.user.IUserCoreService;
import com.cn.user.constants.ResponseCodeEnum;
import com.cn.user.dto.CheckAuthRequest;
import com.cn.user.dto.CheckAuthResponse;
import lombok.extern.slf4j.Slf4j;
import org.apache.commons.lang.StringUtils;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

import javax.annotation.Resource;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.lang.reflect.Method;

/**
 * @author SongQingWei
 * @date 2018/11/29 17:32
 */
@Slf4j
public class TokenInterceptor extends HandlerInterceptorAdapter {

    @Resource
    private IUserCoreService iUserCoreService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        Object bean = handlerMethod.getBean();
        // 判断是否允许匿名访问
        if (isAnonymous(handlerMethod)) {
            return true;
        }
        // 判断是否继承 BaseController
        if (!(bean instanceof BaseController)) {
            throw new RuntimeException(bean.getClass().getName() + "must extend BaseController");
        }
        // 校验token
        String token = CookieUtil.getCookieValue(request, MallWebConstant.ACCESS_TOKEN);
        boolean isAjax = isAjax(request);
        if (StringUtils.isBlank(token)) {
            if (isAjax) {
                response.setContentType("text/html;charset=UTF-8");
                response.getWriter().write("{\"code\":\"-1\",\"msg\":\"error\"}");
                return false;
            }
            response.sendRedirect(MallWebConstant.MALL_SSO_ACCESS_URL);
            return false;
        }
        CheckAuthRequest checkAuthRequest = new CheckAuthRequest();
        checkAuthRequest.setToken(token);
        CheckAuthResponse checkAuthResponse = iUserCoreService.validToken(checkAuthRequest);
        if (ResponseCodeEnum.SUCCESS.getCode().equals(checkAuthResponse.getCode())) {
            BaseController baseController = (BaseController) bean;
            baseController.setUserId(checkAuthResponse.getUserId());
            return super.preHandle(request, response, handler);
        }
        if (isAjax) {
            response.setContentType("text/html;charset=UTF-8");
            response.getWriter().write("{\"code\":\"" + checkAuthResponse.getCode() + "\"" +
                    ",\"msg\":\"" + checkAuthResponse.getMsg() + "\"}");
            return false;
        }
        response.sendRedirect(MallWebConstant.MALL_SSO_ACCESS_URL);
        return false;
    }

    /**
     * 判断是否存在Anonymous注解
     * @param bean HandlerMethod
     * @return boolean
     */
    private boolean isAnonymous(HandlerMethod bean) {
        Object beanBean = bean.getBean();
        Class<?> aClass = beanBean.getClass();
        if (aClass.getAnnotation(Anonymous.class) != null) {
            return true;
        }
        Method method = bean.getMethod();
        return method.getAnnotation(Anonymous.class) != null;
    }
    
    /**
     * 是否是Ajax请求
     * @param request HttpServletRequest
     * @return boolean
     */
    private boolean isAjax(HttpServletRequest request) {
        String requestType = request.getHeader("X-Requested-With");
        return StringUtils.isNotBlank(requestType) && "XMLHttpRequest".equals(requestType);
    }
}
```

