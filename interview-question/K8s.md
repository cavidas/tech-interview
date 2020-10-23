1，K8S 或者任何发布的时候，如果ingress不能及时变更，一部分流量还流向老的部署怎么办？如何避免？see [here](../../../calvin-learning-plan/Tech/K8s.md)
https://blog.csdn.net/lms1719/article/details/48057045
k8s 平滑发布
https://www.cnblogs.com/justmine/p/8688828.html

一般不会，k8s流程上形成闭环了的。 采用冗余更新策略，即先创建，再删除。新创建的实例会马上接受流量，而被标记删除的实例并不会马上删除，只是不分发流量给它而已，一个平滑期后（一般为几十秒），即等它把余下的请求都处理完，才会被删掉。