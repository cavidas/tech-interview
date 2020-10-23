7. 如果多个服务加载一个东西，之后同时开始处理，如何做到？
    1. 可以在加载的消息里标记开始时间。追问如何让服务自己决定时间？
    2. 可以定期scan and check？ 之后制定开始时间。 
    3. Maybe redis存锁，所有任务去取token，之后不断访问redis，当没有可用token的时候就可以开始进行了
    https://blog.csdn.net/hao134838/article/details/77435489
    https://blog.csdn.net/sinat_25295611/article/details/80420086
    https://juejin.im/entry/6844903988798685197