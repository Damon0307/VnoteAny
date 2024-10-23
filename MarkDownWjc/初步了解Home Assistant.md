# 初步了解Home Assistant
![HA](vx_images/489223704903978.png)
## 简介
Home Assistant 是一个开源的智能家居平台，旨在通过集成各种智能设备和服务，提供一个统一的、可自定义的家庭自动化解决方案。它可以允许用户监控、控制和自动化家中的各种设备，包括灯光、温度、安全系统、多媒体设备等等。 一个接入和控制各类智能设备的**<mark>大型中间件</mark>**。
## More Than Lights
 为了与不同品牌和类型的智能设备通信，HomeAssistant 支持多种网络协议和 API，如 MQTT、HTTP、WebSocket 等。这些协议允许 HomeAssistant 与设备进行双向数据交换。 不仅仅控制一下简单的灯光设备，包括车辆，摄像头等复杂设备都可以集成使用。 不同类型设备的互联互通往往有着不同的行业标准。这意味着HA 应该是一个非常复杂庞大的平台。绝不是像一个普通的APP通过简单的交叉编译几个依赖库就可以移植。 通常运行在标准的发行版系统上。
[Onvif](https://www.home-assistant.io/integrations/onvif/)
[matter](https://www.home-assistant.io/integrations/matter/)

![各大平台支持](vx_images/478608693954715.png =693x)
![](vx_images/440963811186683.png =174x)![Car](vx_images/79924648679920.png =735x)
# 安装方式
1. 官方硬件 HomeAssistant Green /  Home Assisatant Yellow
![HA Green](vx_images/247003317689214.png =723x)
![硬件参数](vx_images/253971443069891.png =684x)
99美元的官方硬件，接入本地局域网以后自动发现设备并添加。 通过APP或者 web 操作
[HA green](https://www.home-assistant.io/green)
2. 官方镜像 仅限于少数知名硬件   目前只看到树莓派 
[针对树莓派硬件提供了官方镜像](https://www.home-assistant.io/installation/raspberrypi)
3.  linux虚拟机(VM /Virtual BOX ) + 定制镜像 + 官方docker镜像
![虚拟机最低要求](vx_images/234943516801013.png =485x)
通过该方式启动linux 虚拟机以后再通过Docker安装官方HA镜像
[linux 安装方式](https://www.home-assistant.io/installation/linux)

# 版本对比
![版本对比](vx_images/193867582764319.png =563x)

# 移植在RZG平台的可行性
RZG平台没有对应的OS镜像，所以移植需要用 linux + docker 的方式。假设硬件方便满足接口要求（通讯接口）软件方面理论上瑞萨提供的yocto SDK中包含docker的支持是可以运行 HA的容器。但是过程应该比较艰难，且得不到很好的体验。 



## 参考链接
[科普Home Assistant](https://www.bilibili.com/video/BV1b3ameXEg5?spm_id_from=333.788.videopod.sections&vd_source=5dc074d0943e6a7e367aba2712aa503c)

[香橙派 安装 HA](https://www.bilibili.com/video/BV1Gk4y1Q72Q/?spm_id_from=333.337.top_right_bar_window_history.content.click&vd_source=5dc074d0943e6a7e367aba2712aa503c)
[瀚思彼岸](https://bbs.hassbian.com/)
[万物皆可HA](https://www.bilibili.com/read/cv22291031/)
[摄像头接入HA](https://www.bilibili.com/video/BV1XsCRY4ENc?spm_id_from=333.788.videopod.sections&vd_source=5dc074d0943e6a7e367aba2712aa503c)
[HA 搭建和配置](https://zhuanlan.zhihu.com/p/666917390)