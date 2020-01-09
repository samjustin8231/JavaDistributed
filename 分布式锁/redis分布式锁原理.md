# redis分布式锁原理

redis 最普通的分布式锁
第一个最普通的实现方式，就是在 redis 里使用 setnx 命令创建一个 key，这样就算加锁。

```
SET resource_name my_random_value NX PX 30000

```
执行这个命令就 ok。

NX：表示只有 key 不存在的时候才会设置成功。（如果此时 redis 中存在这个 key，那么设置失败，返回 nil）
PX 30000：意思是 30s 后锁自动释放。别人创建的时候如果发现已经有了就不能加锁了。
释放锁就是删除 key ，但是一般可以用 lua 脚本删除，判断 value 一样才删除：

-- 删除锁的时候，找到 key 对应的 value，跟自己传过去的 value 做比较，如果是一样的才删除。

```
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```
为啥要用 random_value 随机值呢？因为如果某个客户端获取到了锁，但是阻塞了很长时间才执行完，比如说超过了 30s，此时可能已经自动释放锁了，此时可能别的客户端已经获取到了这个锁，要是你这个时候直接删除 key 的话会有问题，所以得用随机值加上面的 lua 脚本来释放锁。

但是这样是肯定不行的。因为如果是普通的 redis 单实例，那就是单点故障。或者是 redis 普通主从，那 redis 主从异步复制，如果主节点挂了（key 就没有了），key 还没同步到从节点，此时从节点切换为主节点，别人就可以 set key，从而拿到锁。