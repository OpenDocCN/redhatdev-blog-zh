# 时区数据(tzdata): 2018 数据格式变化和红帽企业 Linux

> 原文：<https://developers.redhat.com/blog/2019/02/22/time-zone-data-tzdata-2018-data-format-changes-and-red-hat-enterprise-linux>

[Red Hat Enterprise Linux](https://developers.redhat.com/products/rhel/overview/)(RHEL)需要时区信息，以便操作系统中的所有应用程序都能正确打印当地时间。GNU C 库(`glibc`)使用了`tzdata`包，以便让像`strftime()`这样的 API 正常工作，而像`/usr/bin/date`这样的应用程序则利用这些信息来打印本地日期。

`tzdata`包包含记录全球不同时区的当前和历史转换的数据文件。此数据代表了当地政府机构或时区边界变化所要求的更改，以及 UTC 偏移量和夏令时(DST)的更改。

本文描述了 2018 年推出的三种`tzdata`时区数据格式的变体，以及 tzdata 将如何在 Red Hat Enterprise Linux 中进行更改。

偶尔，`tzdata`在没有很多交付时间或信息不完整的情况下宣布变更。在这些情况下，我们尝试为不同的场景做计划，并依靠上游维护人员根据他们的经验做出决定。一旦更新准备就绪，我们会更新我们的源代码，我们的质量保证和发布工程师会尽快帮助推出更新。

IESG(互联网工程指导小组)指定的 TZ 协调员是保罗·埃格特。定期或根据正确性要求，上游`tzdata`更新在【iana.org 网站上发布。你还可以在这个网站上找到关于订阅`tzdata`电子邮件列表和访问档案的信息。

2018 年，上游项目推出了三种`tzdata`时区数据格式的变体。所有这三种格式都准确地反映了正确的最新时区数据。三种数据格式是*前锋、主力和后卫*。

*vanguard* 格式包含最新的数据格式特性。在某些情况下，这些新的格式变化可能会破坏其他使用自己的解析器来解析时区数据文件的包。

*main* 格式旨在一段时间后提供新功能。目前，vanguard 和 main 都包含 2018 年推出的新功能。

*后卫*格式的目的是在要求支持新的数据格式特性之前提供更长的时间。

2018 年引入的两个突出的数据格式功能是使用负 DST 值和引入 25:00 时间间隔，以前是 24:00。众所周知，这两项更改会破坏一些提供自己的解析器来解析时区数据文件的应用程序。协调识别、更新和运送受这些格式变化影响的包所需的时间会严重影响我们及时运送关键`tzdata`更新的能力。

经过大量的研究和考虑，Red Hat Engineering 认为，对我们的客户和软件包提供商来说，最好的选择是推迟引入这些新的格式变化，同时仍然提供准确和及时的更新。因此，RHEL `tzdata`包目前与*后卫*格式一起运送，以允许我们的数据格式消费者有时间将他们的解析器更新到新的格式。

有关更多信息，请参见 [Red Hat Enterprise Linux 时区数据(tzdata) -开发状态页面](https://access.redhat.com/articles/1187353)。