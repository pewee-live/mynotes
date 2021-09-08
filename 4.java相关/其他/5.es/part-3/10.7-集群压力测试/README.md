# 集群压力测试
![](0.png)
![](1.png)
![](2.png)
![](3.png)
![](4.png)
![](5.png)
![](6.png)
![](7.png)
![现存集群压力测试](8.png)

## 课程demo
```
pip3 install esrally

esrally configure

# 只测试 1000条数据
esrally --distribution-version=7.1.0 --test-mode

# 测试完整数据
esrally --distribution-version=7.1.0

```
# 相关阅读
- https://github.com/elastic/rally
- https://github.com/elastic/rally-tracks
- https://logz.io/blog/rally/
