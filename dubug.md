# spring
## 注入 bean 问题
![image.png](https://raw.githubusercontent.com/kira018/img/main/202502191624449.png)

报错信息
Bean named 'redisTemplate' is expected to be of type 'org.springframework.data.redis.core.StringRedisTemplate' but was actually of type 'org.springframework.data.redis.core.RedisTemplate'
### redis
首先我们需要知道，redis 自动就会注入 bean 不需要配置config 类，如下图。
![image.png](https://raw.githubusercontent.com/kira018/img/main/202502191551666.png)
图片显示里我没有配置 redisConfig，但还是能获取到 bean，说明有人在帮我们做这个事情，点进 bean 的管理看到有个RedisAutoConfiguration类会帮你自动注入
所以不可能是因为没有注入 bean才导致的，那么问题就出在别的地方。
报错信息说他期望找到一个类型为StringRedisTemplate的对象，但是实际找到的是RedisTemplate，从这个报错信息得知，我们实际上找到的bean 是 RedisTemplate，这是为什么呢？

原因是@Resource注解默认根据名称装配byName，未指定name时，使用属性名作为name也就是这里的redisTemplate。通过name找不到的话会自动启动通过类型byType装配。
所以这里实际上是根据我的属性名去找 bean 了。换个属性名，问题解决。


# git
## git 分支出现提交记录特别多
这个原因是因为你当前的分支很早拉的没有进行更新 pull 或者 feacch ，如果当前分支已经是最新的，那么可能是因为远程分支落后于本地分支。
