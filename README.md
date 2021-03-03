

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

 - heck_at_destination：查看已经到达目的地的人**

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
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先，模拟随机移动的人群。人们停留在世界范围之内，每走动一下，他们就有2％的机会改变前进方向和速度。与被感染者接近时，有3％的机会生病，而致命结局的可能性则为2％。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;然后将随着年龄增长而增加的风险纳入其中，并简单构建有限能力的医疗保健系统。两者都影响大流行期间的死亡率：老年人很脆弱，一旦医疗保健系统不堪重负，很多人由于缺乏治疗而死亡的风险增加。下列参数处于活动状态（所有参数均可设置，也可以设置过程）：

-   人口的年龄遵循高斯分布，平均数为55，标准差为平均值的1/3，最大值为105

-   人口包括2000个人
-   在被感染者附近时有3％的机会被感染
-   基线死亡率为2％
-   死亡率机会在55岁时开始增加，在75岁及以后的年龄段呈指数增长，最高可达10％
-   医疗保健能力为300张床
-   就医时：死亡几率减半
-   当没有在医院内治疗：死亡几率增加三倍。

## 运行结果

 场景1. 照常生活
第一个模拟运行显示了人们只是继续做正常的事情四处走动。从下面的结果中可以看到，医疗保健系统完全不堪重负，导致215人死亡
![enter image description here](https://github.com/xhangchen/Virus-simulation/blob/main/images/business_as_usual.png)
