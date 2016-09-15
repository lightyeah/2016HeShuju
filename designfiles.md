谱仪系统  
==========================
*2016秋*
--------------------------
***
## 软件  
*——"罗洋"*
### 需求及功能分析
* 基本功能  
    - 数据归档保存阅览 *打开、浏览、查找、保存（格式设置）*
    - 能谱数据读取 *采集控制（计数方式、通道设置、ADC参数设置）、开始/停止读数*
    - 能谱数据处理 *道址细分、数据存储、寻峰、积分、半高宽*
    - 能谱数据显示 *1024(能谱精度)、计数量程（自动、手动、线性、对数）*
    - 用户动作 *峰识别、放大/缩小、感兴趣区、光标动作*
* 高级功能
    - 数据高级处理 *核素识别、活度计算、本底扣除*
    - 数据显示 *画面防抖防闪、能谱平滑处理、静态/动态显示*
    - 用户设置 *能谱颜色*
    - 界面优化
    - 错误信息
### 软件规划
![软件架构图](https://raw.githubusercontent.com/lightyeah/2016HeShuju/master/1.jpg)  

* 界面分区（ui） 
    - 工具栏  
        - 顶栏    
        *文件* -> *打开* |*保存* |*另存为* |*退出*  
        *数据采集* -> *选择端口* |*开始采集* |*暂停* |*停止*  
        *数据显示* -> *静态显示* |*动态显示* |*刷新* |*量程设置*->*手动* |*自动*  
        *数据处理* -> *平滑处理* |*数据输出* |*显示峰位*  
        *下位机控制* -> *采集方式* -> *计数* |*计时* ||*参数设置*  
        - 快捷键  
        *打开*  
        *存储*  
        *另存为*  
        *连接*  
        *刷新绘图*  
        *开始绘图*  
        *暂停*  
        *停止*  
        *清除*  
    - 控制面板  
        - *工作方式* -> *定时选择* & *时间/s* |*定数选择* & *个数*  
        - *绘图参数设置* -> *道址设置* -> *道宽* & *起始道址* & *终止道址*  
                         -> *计数量程* -> *手动量程参数输入*   
        - *数据采集* -> *选择通道*                     
    - 数据显示  
        *工作时间*  
        *工作总计数*  
        *最高峰位* & *最高峰计数* & *最高峰半高宽*  
        *感兴趣区* -> *局部最高峰位*&*局部最高峰计数* |*局部总计数* |*局部半高宽*
    - 绘图区 
        - 绘图 *在内存上绘图，传递图片，解决闪烁问题*  
        - 平滑处理图像，在图上计算半高宽
        - 处理数据 得到数据显示所需要的数据
* 数据处理(dataprocessor)  
    - 多线程任务：数据读取存储和绘图调用分开处理，绘图每1s更新一次，但是数据读取一直在后台运行
    - 从下位机获取数据 *在内存有一个队列，存储临时用的数据*
    - 传递数据给数据库存储 *也可以调用以前的数据*
    - 给界面传递绘图数据 *传递图片*
    - 从界面获得用户操作信息 *下位机控制参数、绘图参数*
* 数据库（database） 
    - 设定数据格式
    - 存储数据
    - 调用数据
    - 存成其他格式
* 下位机(hardware)  
    - 与硬件通信，获取数据
    - 控制硬件参数  
### 各个模块编程接口  *variable/widget->function*    
* ui  
    - 工具栏
        + private
            * findAllPort();//显示所有可用端口 ！！如何获得设备端口信息？？
            * DataPortSelect -> selectDataPort();//用户选择端口
            * exit()//!!!退出程序之前要注意内存清理和释放
            * clearData();
        + public
    - 控制面板
        + private
            * clearData();
        + public
    - 数据显示 0.1s更新一次数据
        + private
            * CollectWorkTime;
            * TIMER  0.1s
        + public
            * clearData();
    - 绘图 0.1s一次
        + private
            * TotalCounts;//总计数
            * PeakAdress;
            * PeakCounts;
            * PartPeakAdress;
            * PartPeakCounts;
            * PaintTimer;//0.1s一次
        + public
            * getData(name,value);//数据显示调用各种数据
            * clearData();//
* dataprocessor
    - private
        + dataqueue OriginOrderQueue[];//原始数据队列
        + dataarray RankedQueue[];//排列后的数据，按道指排列，绘图需要
    - public
        + userHardwareSetting(hardwareparameter);//用户硬件参数设置
        + selectDataPort(port);//选择端口
        + startCollectData();//开始工作，停止结束由ui决定
        + stopCollectData();//停止数据测量
        + getRankedData(dataarray& returnqueue);// ui 1s一次获取绘图数据 ！！如何设置数据只读指针？？
        + saveDataFile(name,path,filetype);
        + openDataFile(name,path);
        + clearData();
* hardware
    - private
        + openPort(port);
        + DataQueue;//数据做队列处理
        + collectData();
    - public
        + selectDataPort(port);
        + setHardware(hardwareparameter)//设置硬件
        + startCollecData();
        + stopCollecData();
        + getData();
* database
    - private
        + sql //！！数据库管理？？
    - public
        + typedef DataPoint datapoint
        + saveDataFile(name,path,filetype,dataqueue,rankedqueue);
        + openDataFile(path,name,filetype);
    


***  
## 硬件  
*——"叶一锰"*  


