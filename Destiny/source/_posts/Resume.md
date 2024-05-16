异步电路EDA
参与兰州大学异步电路与系统实验室（LZU-ARC）的PinTu项目（面向FPGA ASIC的全异步开源EDA工具链），目前开源到启智平台（OpenI，新一代人工智能开源开放平台）。主要完成对项目源码的部分分析工作，形成介绍文档。对整个项目的整体介绍及部署开源工作。



Gitlet
Gitlet是一个版本控制系统，实现了git的一些特性和功能，相比git在部分功能和实现上进行了简化。支持add、commit、log、checkout、merge等本地仓库操作，同时实现push、fetch、pull等远端仓库命令操作。利用java序列化的方法实现commit和stage对应数据结构的持久化存储，通过sha1算法计算相应的哈希值实现内容可寻址。实现字典树存储commit哈希值，实现根据6位前缀快速查找对应的commit哈希值。





主要包括blob，commit，stage，每一个文件对应一个blob对象，commit和stage分别用于提交和暂存区的对象。

WebServer
实现简易的web服务器，
