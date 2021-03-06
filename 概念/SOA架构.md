# SOA架构
当某一天使用单体架构发现很难推进需求的开发、以及日积月累的技术债，很多企业会开始做单体服务的拆分。
拆分的方式一般有水平拆分以及垂直拆分。
**垂直拆分**
把一个应用拆成松耦合的多个独立的应用，让应用可以独立部署，有独立的团队进行维护。
**水平拆分**
把一些通用的，会被很多上层服务调用的模块独立拆分出去，形成一个共享的基础服务，这样拆分可以对一些性能瓶颈的应用进行单独的优化和运维管理，也一定程度防止了垂直拆分的重复造轮子。
（个人理解普日口腔的基础服务就是这样来的）



SOA也叫面向服务的架构，从单体服务到SOA的演进，需要结合水平拆分以及垂直拆分。SOA强调用统一的协议进行服务间的通信，服务间运行在彼此独立的硬件平台但是需通过统一的协议接口相互协作，也即将应用系统服务化。举个易懂的例子，单体服务如果相当于一个快餐店，所有的服务员都是一样的，又要负责收银结算，又要负责做汉堡，又要负责端盘子，又要负责打扫，服务员之间不需要有交流，一个用户来了一个服务员从前到后负责到底。SOA相当于让服务员有职责分工，收银员负责收银，厨师负责做汉堡，保洁阿姨负责打扫等等。所有服务员需要同一种语言交流，方便工作协调。