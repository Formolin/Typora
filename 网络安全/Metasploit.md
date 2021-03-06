# Metasploit

Metasploit 是一个开源的渗透测试软件，也是一个逐步发展成熟的漏洞研究与渗透测试代码开发平台（可扩展），此外也将成为支持整个渗透测试的安全技术集成开发与应用环境。

Metasploit 项目最初是由HD Moore 在2003年夏季创立的，目标是成为渗透测试攻击研究与代码开发的一个开发环境

HD Moore 在2003年 发布了他的第一个基于Perl 语言（pl）的Metasploit 版本，当时一共集成了11个渗透攻击模块

在Spoonm 的帮助下，完全重写了Metasploit 的代码，并在2004年发布了Metasploit v2.0， 版本中包含了18个渗透攻击模块和27个攻击载荷（payload），并提供了控制台终端，命令行和Web 三个使用接口

2004年 8月，HD 和Spoonm 带着最新发布的Metasploit v2.2 并在拉斯维加斯举办的BlackHat 全球黑客大会上进行了演讲。听众被Metasploit 的强大之处所折服，并一致认为：Metasploit 时代已经到来。更多的黑客加入Matesploit 核心开发团队与贡献渗透攻击、载荷与辅助模块代码。

## 技术架构

### 辅助模块

Metasploit 为渗透测试的信息搜集环节提供了大量的辅助模块支持，包括针对各种网络服务的扫描与查点、构建虚假服务收集登录密码、口令猜测破解、敏感信息泄露、Fuzz 测试发掘漏洞、实施网络协议欺骗等模块。

辅助模块能够帮助渗透测试攻击者在渗透攻击之前取得目标系统丰富的情报信息

### 渗透攻击模块

渗透攻击模块是利用发现的安全漏洞或配置弱点对目标系统进行攻击，以植入和运行攻击载荷，从而获取对远程目标系统访问权的代码组件。

**主动渗透攻击**所利用的安全漏洞位于网络服务端与服务承载的上传应用程序中，由于这些服务通过是在主机上开启一些监听端口等待客户端连接，因此针对它们的渗透测试攻击可以主动发起，通过连接目标系通网络服务，注入一些特殊构造的包含“邪恶”的攻击数据的网路请求内容，触发俺去那漏洞，并设定远程服务进程执行在“邪恶”数据中包含的攻击载荷，从而获取目标系统的控制会话。

**被动渗透攻击**利用的漏洞为于客户端软件中，如浏览器、浏览器插件、点击邮件客户端、Office与Adobe 等各种文档阅读与编辑软件。对于这类存在于客户端软件的安全漏洞，我们无法主动的将数据从远程输入到客户端软件中，因此只能采用被动渗透攻击的方式，即构造出“邪恶”的网页、电子邮件或文档文件，并通过架设包含此类恶意内容的服务、发送邮件附件、结合社会工程学分发并诱骗目标用户打开、结合网路欺骗和劫持技术等发式，等目标系统上的用户访问到这些“邪恶”的内容，从而触发客户端软件中的安全漏洞，给出控制目标系统的Shell 会话。

### 攻击载荷

攻击载荷是在渗透测试攻击成功后使目标系统运行的一段植入代码，通常作用是为渗透攻击者打开在目标系统上的控制会话连接

### 空指令模块

空指令（NOP）是对一些程序运行状态不会造成任何实质影响的空操作或者无关操作指令，最典型的空指令就是空操作，在x86 CPU 体系架构平台上的操作码是0x90

### 编译器模块

攻击载荷模块于空指令模块组装完成一个指令序列后，在这段指令被渗透攻击模块加入“邪恶”数据缓冲区交由目标系统运行之前，Metasploit 框架还需要完成一道非常重要的工序--编码

编译器模块的第一个使命就是确保攻击载荷中不会出现渗透攻击过程中应加以避免的“坏字符”。

第二个使命就是对攻击载荷进行”免杀“处理。

###  后渗透攻击模块

后渗透攻击模块主要支持在渗透攻击取得目标系统控制权之后，在受控系统中进行各式各样的后渗透攻击动作，比如获取敏感信息、进一步拓展、实施跳板攻击等

### 免杀模块

对攻击载荷进行“免杀”处理。

## 为什么使用Metasploit ?

渗透攻击是目前Metasploit 最强大和最具吸引力的核心功能，Metasploit 框架中集成了数百个针对主流操作系统平台上 不同网路服务于应用软件安全漏洞的渗透攻击模块，可以由用户在渗透攻击场景中根据漏洞扫描结果进行选择，并能够自由装配该平台上适用的具有指定功能的攻击载荷，然后通过自动化编码机制绕过攻击限制与检测，对目标系统实施远程攻击。获取系统的访问控制权。

除了渗透攻击之外，Metasploit 在发展过程中逐渐增加了对渗透测试全过程的支持，包括情报搜集、威胁建模、漏洞分析、后渗透攻击与报告生成。

 

### @ 情报搜集阶段

Metasploit 一方面通过内建的一系列扫描器与查点辅助模块来获取远程服务器信息，另一方面通过插件机制集成调用Nmap、Nessus、OpenVAS 等著名业界的开源网路扫描工具，从而具备全面的信息搜集能力，为渗透攻击实施提供必不可缺德精确情报。

 

### @威胁建模阶段

在搜集信息之后，Metasploit 支持一系列数据库命令操作直接将这些信息汇总至PostgreSQL、MYSQL、SQLite数据库中，并为用户提供易用的数据库查询命令，可以帮助渗透测试者对目标系统搜到的情报进行威胁建模，从中找出可行的攻击路径

 

### @ 漏洞分析阶段

除了信息搜集环节能够直接扫描出一些已公布德安全漏洞之外，Metassploit 中还提供了大量德协议Fuzz 测试器与Web 应用漏洞探测分析模块，支持具有一定水平能力德渗透测试者在实际过程中尝试挖掘出0day 漏洞，并对漏洞机理与利用方法进行深入分析，而这将为渗透攻击目标带来更大的杀伤力，并提升渗透测试流程的技术含金量。

 

### @后渗透攻击阶段

在成功实施渗透攻击并获取目标系统的远程控制权之后，Metasploit 框架中另一个极具威名的工具Meterpreter 在后渗透攻击阶段提供了强大的功能。

Meterpreter 可以看作一个支持多操作系统平台，可以 仅仅驻留于内存中并具备免杀能力的高级后门工具，Meterpreter 中实现了特权提升、信息抓取、系统监控、跳板攻击与内网拓展等多样化的功能特性，此外还支持一种灵活可扩展的方式来加载额外的功能的后渗透攻击模块

 

### @ 报告生成阶段

Metssploit 框架获得的渗透测试结果可以输入至内置数据库中，因此这些结果可以通过数据库查询来获取，并辅助渗透测试报告的写作。

商业版（pro）具备了更强大的报告生成功能，可以输出HTML、XML、Word 和PDF格式的报告。

