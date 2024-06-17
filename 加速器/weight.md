参数含义：

*`COMPUTE_CHANNEL_IN_NUM` : 可以同时计算的输入通道数（假设为8）*

*`COMPUTE_CHANNEL_OUT_NUM` : 可以同时计算的输出通道数（假设为8）*

*`WEIGHT_S_DATA_DEPTH` : 取所有层中 Max(C x M / 8) *

*`channelIn` : 本层卷积输入通道数（记为 C）*

*`channelOut` : 本层卷积输出通道数（记为 M）*

*`weightNum` : 一个像素点对应的全部权重读取次数  = C x M / 8*

*`quanNum` : 一个卷积核对应的参数读取次数  =  M x 4 / 8  (一个参数是32bit，4个字节)*

