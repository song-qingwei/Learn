```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;
import org.springframework.util.StringUtils;

import javax.annotation.Resource;

/**
 * @description: Redis 加锁与解锁
 * @author: SongQingWei
 * @create: 2018-03-13 20:55
 */
@Service
@Slf4j
public class RedisLockServiceImpl implements RedisLockService {

    private static final String LOCK_PREFIX = "lock:";
    
    @Resource
    private StringRedisTemplate stringRedisTemplate;

    /**
     * 加锁（确保锁超时时间大于任务执行时间）
     * @param lockName 锁名称
     * @param acquireTimeOut 获得锁的超时时间
     * @param lockTimeOut 锁本身的过期时间
     * @return 成功返回锁值，失败返回null
     */
    public String acquireLock(String lockName, long acquireTimeOut, long lockTimeOut) {
        log.info(Thread.currentThread().getName() + "尝试获取锁...");
        String lockKey = LOCK_PREFIX.concat(lockName);
        String identifier = UUID.randomUUID().toString();
        // 获取锁的限定时间
        long end = System.currentTimeMillis() + acquireTimeOut;
        while (System.currentTimeMillis() < end) {
            // 尝试获取锁
            Boolean ifAbsent = stringRedisTemplate.opsForValue().setIfAbsent(lockKey, identifier);
            if (ifAbsent != null && ifAbsent) {
                stringRedisTemplate.expire(lockKey, lockTimeOut, TimeUnit.MILLISECONDS);
                return identifier;
            }
            // 保证代码健壮
            Long expire = stringRedisTemplate.getExpire(lockKey, TimeUnit.MILLISECONDS);
            if (expire == null || expire == -1) {
                stringRedisTemplate.expire(lockKey, lockTimeOut, TimeUnit.MILLISECONDS);
            }
            try {
                TimeUnit.MILLISECONDS.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        log.info(Thread.currentThread().getName() + "请求锁超时，获得锁失败");
        return null;
    }

    /**
     * 释放锁
     * @param lockName 锁名称
     * @param identifier 锁值
     */
    public void releaseLock(String lockName, String identifier) {
        String lockKey = LOCK_PREFIX.concat(lockName);
        String currentValue = stringRedisTemplate.opsForValue().get(lockKey);
        // 如果当前key已超时，则value可能会被重写，可能会导致误删
        if (!StringUtils.isEmpty(currentValue) && currentValue.equals(identifier)) {
            stringRedisTemplate.delete(lockKey);
        }
    }
}
```

