# KF-GINS NOTE

1. `types.h`定义了GNSS、IMU、Pose三个结构体；

2. `angle.h`类定义了弧度角度转换的成员方法；

3. `GIEngine`类：组合导航工程类，`GIEngine` 内部集成了惯性导航（INS）机械编排、卡尔曼滤波（EKF）的状态传播与更新、IMU误差补偿、GNSS数据融合等复杂功能（如 `insPropagation`、`EKFPredict`、`gnssUpdate` 等方法）。

   - 公开方法：

   - 提供成员方法添加IMU数据
   - 添加GNSS数据
   - 处理新的IMU数据
   - IMU数据内插
   - 获取当前时间
   - 获取当前导航状态
   - 获取当前协方差阵
   - 私有方法：
   - 初始化导航状态
   - IMU数据补偿
   - 判断是否需要更新状态
   - INS状态更新
   - GNSS量测更新
   - EFK预测
   - EKF量测更新
   - 反馈状态误差到当前状态

4. 文件输入输出`filebase`类：通过引入`<fstream>`类实现文件的关闭，判断是否打开文件、判断是否错误、获取文件列数

5. 文件载入`FileLoader`类：公开继承`filebase`类

   - 构造函数：指定文件名，文件列数，文件类型，并打开文件
   - 提供成员方法`bool FileLoader::open()`：首先判断文件类型，若文本文件，则设置为仅读文本模式；若为二进制文件，设置为只读＋二进制模式
   - 提供成员方法`vector<double> load()`：调用`load_()`函数，返回数据data_
   - 提供成员方法`vector<vector<double>> loadn(int epochs)`：加载前epoch历元的数据
   - 提供成员方法`bool load(vector<double> &data)`：将私有成员变量`data_`的值通过移动操作返回给data
   - 提供成员方法
   - 提供成员方法`bool load_()`：将文件内容载入到成员变量`data_`中





1. 文件操作的基本流程：
   创建流式对象 打开文件 读写数据 关闭文件