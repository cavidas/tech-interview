https://zhuanlan.zhihu.com/p/37530954
https://support.google.com/analytics/answer/6148697

# 启发式归因 - heuristic attribution

https://support.google.com/analytics/answer/1662518?hl=zh-Hans

“最终互动”模型图标 在最终互动归因模型中，最后一个接触点（在本例中为“直接”渠道）将获得 100% 的销售功劳。
 
“最终非直接点击”模型和“最终 Google Ads 点击”模型图标 在最终非直接点击归因模型中，所有直接流量均被忽略，100% 的销售功劳归于客户在转化之前点击访问的最后一个渠道（在本例中为“电子邮件”渠道）。
 
“最终非直接点击”模型和“最终 Google Ads 点击”模型图标 在最终 Google Ads 点击归因模型中，最终 Google Ads 点击（本例中为对“付费搜索”渠道的首次也是唯一一次点击）将获得 100% 的销售功劳。
 
“首次互动”模型图标 在首次互动归因模型中，第一个接触点（在本例中为“付费搜索”渠道）将获得 100% 的销售功劳。
 
“线性”模型图标 在线性归因模型中，转化路径中的每个接触点（在本例中为“付费搜索”、“社交网络”、“电子邮件”和“直接”渠道）将平分销售功劳（每个均获得 25%）。
 
“时间衰减”模型图标 在时间衰减归因模型中，最接近销售或转化时间的接触点将获得最多的功劳。在这次销售中，“直接”和“电子邮件”渠道将获得最多的功劳，因为客户在转化前的几个小时与之进行了互动。“社交网络”渠道获得的功劳将少于“直接”或“电子邮件”渠道。由于“付费搜索”互动是在一周之前发生的，因此该渠道获得的功劳比另外几项少得多。
 
“根据位置”模型图标 在根据位置归因模型中，将向首次互动和最终互动各分配 40% 的功劳，剩下 20% 的功劳将平均分配给中间的互动。在本例中，“付费搜索”和“直接”渠道各自获得 40% 的功劳，而“社交网络”和“电子邮件”渠道各自获得 10% 的功劳。


# 算法归因 - algorithm attribution
1. logistic归因模型
2. 基于生存分析的归因模型-cox模型
3. 其他归因模型
算法归因除了上面介绍的两个模型外，常见的模型还有probabilistic模型（《A Probabilistic Multi-Touch
Attribution Model for Online Advertising》）和基于markov过程的模型（《Mapping the Customer Journey: A Graph-Based Framework for Online
Attribution Modeling》）。我们可以直接调用R包ChannelAttribution（https://cran.r-project.org/web/packages/ChannelAttribution/index.html）基于markov过程计算各渠道贡献度。