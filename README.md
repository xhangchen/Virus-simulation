

## 代码思路

 仿真过程与画板绘制采用模块化实现，各文件内容如下：
 
[environment.py](https://github.com/xhangchen/Virus-simulation/blob/main/environment.py):包含仿真环境中所有能到达位置的函数定义，

 - build_hospital：建立医院并返回它的坐标

[Config.py](https://github.com/xhangchen/Virus-simulation/blob/main/config.py) :包含所有与配置相关的函数和类

 - Configuration类：包含仿真变量，方案标志，可视化变量，以坐标表示的模			拟世界的大小，范围变量，人口变量，行动变量，感染变量，医疗保健变量，风险参数，自我隔离变量，锁定变量，自定义各类变量初值的函数，

[Infection.py](https://github.com/xhangchen/Virus-simulation/blob/main/infection.py):包含计算新感染、恢复和死亡所需的所有函数

 - find_nearby:确定在感染区内是否返回感染者或健康者
 - Infect:在受感染者周围由感染范围定义的区域发现新的感染，并用偶然感染机会感染其他人的功能
 - recover_or_die:判断患者恢复还是去世。
   
 
 - compute_mortality：根据年龄计算死亡率和风险
 - healthcare_infection_correction：纠正对医疗保健人群的感染

[Motion.py](https://github.com/xhangchen/Virus-simulation/blob/main/motion.py):包含与人口流动性相关的所有函数的文件

 - update_positions:使用航向和速度为下一时间步更新所有人的位置
 - out_of_bounds：检查哪些人将要越界，并更正更新将要越界的个人航向

 - update_randoms：更新航向和速度等随机状态

[Path_planning.py](https://github.com/xhangchen/Virus-simulation/blob/main/path_planning.py):包含与目标导向的移动行为和路径规划相关的方法

 - go_to_location：将病人送往指定位置

 - set_destination：定义人群的目的地

 - heck_at_destination：查看已经到达目的地的人

 - keep_at_destination：把那些已经到达的人留在游荡的范围内

 - reset_destinations：重置目的地

 - Population:包含有助于模拟参数初始化的填充函数

 - initialize_population:为仿真初始化人群

 - initialize_destination_matrix：初始化目的地矩阵

 - set_destination_bounds：传送限制范围内的所有人

 - save_data：将模拟数据转储到磁盘

 - save_population：将给定时间段的总体数据转储到磁盘

 - Population_trackers类：可以跟踪随时间变化的总体参数，然后用于计算统计数据或可视化。

[Visualiser.py](https://github.com/xhangchen/Virus-simulation/blob/main/visualiser.py):包含可视化任务的所有函数

 - draw_tstep：构建图像和可视化

 - plot_sir：在总体跟踪器中绘制参数

[Simulation.py](https://github.com/xhangchen/Virus-simulation/blob/main/simulation.py):包含仿真类和仿真程序的主函数

 - reinitialise：重置仿真过程

 - population_init：人群初始化

 - tstep：在模拟中前进一个时间步长

 - run：执行仿真程序
 
## 部分设定
首先，模拟随机移动的人群。人们停留在世界范围之内，每走动一下，他们就有2％的机会改变前进方向和速度。与被感染者接近时，有3％的机会生病，而致命结局的可能性则为2％。

然后将随着年龄增长而增加的风险纳入其中，并简单构建有限能力的医疗保健系统。两者都影响大流行期间的死亡率：老年人很脆弱，一旦医疗保健系统不堪重负，很多人由于缺乏治疗而死亡的风险增加。下列参数处于活动状态（所有参数均可设置，也可以设置过程）：

-   人口的年龄遵循高斯分布，平均数为55，标准差为平均值的1/3，最大值为105

-   人口包括2000个人
-   在被感染者附近时有3％的机会被感染
-   基线死亡率为2％
-   死亡率机会在55岁时开始增加，在75岁及以后的年龄段呈指数增长，最高可达10％
-   医疗保健能力为300张床
-   就医时：死亡几率减半
-   当没有在医院内治疗：死亡几率增加三倍。

## 运行结果

 **场景1. 照常生活**
第一个模拟运行显示了人们只是继续做正常的事情四处走动。从下面的结果中可以看到，医疗保健系统完全不堪重负，导致196人死亡(占人口的9.8％)。

![enter image description here](https://github.com/xhangchen/Virus-simulation/blob/main/images/business_as_usual.png)

 **场景2. 减少接触**
 第二个模拟具有相同的设置，但是要模拟人们尽可能呆在家里并且仅在必要时才出门，大大降低了流动性。如图所示，尽管在某些时候医疗保健能力被高估，但对死亡率的影响仍然很低，只有39个致命结局（1.95％）。死亡人数几乎为原来的1/5。

![enter image description here](https://github.com/xhangchen/Virus-simulation/blob/main/images/reduced_interaction.png)

 **场景3. 封锁位置**
 模拟一旦5％的人口受到感染时的就会进入锁定状态。一旦锁定，将使90％的人停止移动，其余10％的人将以大大降低的速度移动，以模拟他们更加谨慎。这10％代表了对社会至关重要的职业：这些人即使处于锁定状态，也将继续前进并与其他人保持联系。10％的另一部分来自于破坏纪律的人，这模拟着没有绝对的封锁。

可以看出一旦锁定，感染数量仍会增加一段时间。之所以会发生这种情况，是因为一些健康的人会与感染者被锁在同一个家庭中，因此也很快就会被感染。如果移动人口中的一个成员（也许是邮递员或运送食品的人）感染了一群被关在一起的人，则该疾病可能会传播。这导致了较小且孤立的爆发，通过锁定可以很好地控制爆发。但是，如果解除锁定并引入新的情况，那么如果不采取适当的措施，则可能会导致潜在严重情况：感染继续复发，就会导致反复锁定。

![enter image description here](https://github.com/xhangchen/Virus-simulation/blob/main/images/lock_down.png)


 **场景4. 自我隔离**
 另一种方法是自我隔离：指导有症状的人呆在家里。在模拟中，被感染的人会自我隔离。那些前往隔离区的人无法再感染其他人，以模拟这些人已经意识到自己的感染，并将采取预防措施以不感染他人。从结果可以看出如果潜伏期较长，更多的情况将是感染者在自知感染之前已经传染了其他人，于是导致疫情爆发，但相比感染后照常行动而言，结果还是良好很多。

![enter image description here](https://github.com/xhangchen/Virus-simulation/blob/main/images/self_isolation.png)

## 结论
在疫情期间，实施减少接触、封锁地方、医疗条件不足情况下的自我隔离等措施都能起到一定的疫情防控效果。

##


>  参靠项目：https://github.com/paulvangentcom/python_corona_simulation#case-reduced-interaction
