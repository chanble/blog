含开始时间结束时间的节点权限有效期问题
============================================================

申请角色或节点权限允许多次申请， 还允许一个有效期内进行再次申请，多次申请会出现有效期交叉的情况。
可能出现的情况如下：
```
                    ______         _________
                   |      |       |         |
1. 顺序情况： .....a------a'.......b---------b'.....  时间线——>
                    ________________________
                   |                        |
2. 包含情况： .....a------b--------b'--------a'.....  时间线——>
                          |        |
                           --------
                   ________________
                  |                |
3. 交叉情况： .....a------b--------a'--------b'.....  时间线——>
                          |                  |
                           ------------------
```
**注：** ... 表示有效期的申请未生效期间  ---表示生效期间  a表示申请的开始生效时间点 a'表示对应a的失效时间点

## 总体逻辑
每个申请是一个生效失效对儿，生效和失效分别有一个生效状态和失效状态。每个申请失效状态完成后，对应的生效状态不一定会完成(此句话非常重要)。
每个申请失效状态先完成，生效状态后完成，生效状态完成后，此申请结束。
对于情况1(顺序)和情况2(包含)可以正常处理，对于情况3(交叉)比较复杂。
每次生效时不影响继续生效，每次生效时记录此次生效添加的内容和删除的内容，然后生效。如果失效时间到，失效状态完成。对比和最近一次未完成的生效状态，进行差值恢复。将本次失效状态设置为完成，与本次对应的生效状态不变。
如果发现最近一次的生效状态和自己是一对儿，则进行权限恢复，失效本申请。并且继续向前找到所有未完成的生效状态。逐一进行权限恢复。
