# ChainMaker Log Specific

1、统一使用小写，方便运维人员grep

2、时间默认毫秒为单位，如果不是毫秒需标注单位

3、普通INFO日志，使用动宾结构，后面追加关键词/数据，如：put block [124911] (txs:727,bytes:831670), time used (mashal:6,logdb:1,commit:10,total:17)

4、错误日志：*** failed, 错误原因

5、用方括号标识编号类信息，如：[blockNumber]、[nodeid]

6、校验错误类日志：expect XXX, got YYY, use default ZZZ

INFO

0、网络（初始化、新链接、链接断开、链接弃用、配置刷新）

1、txpool（入池、fetch及耗时、remove、retry）

2、打包（打包及耗时）

3、共识（prevote、precommit、commit、newHeight）在with majority时再输出

4、验证（验证及耗时）

5、落块（落块及耗时）

6、vm（一次调度一行结果日志，成功或某一笔失败及原因，包含耗时）