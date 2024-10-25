### 参数含义：

*`DATA_WIDTH ` : 单个权重数据位宽（假设为8bit，1Byte）*

*`COMPUTE_CHANNEL_IN_NUM` : 可以同时计算的输入通道数（假设为8）*

*`COMPUTE_CHANNEL_OUT_NUM` : 可以同时计算的输出通道数（假设为8）*

*`channelIn` : 本层卷积输入通道数（记为 C）*

*`channelOut` : 本层卷积输出通道数（记为 M）*

*`WEIGHT_S_DATA_DEPTH` : 取所有层中 Max(C x M / 8)*

*`weightNum` : 一个像素点对应的全部权重读取次数  = C x M / 8 (8代表可以同时计算的输入通道数)*

*`quanNum` : 一个卷积核对应的参数读取次数  =  M x 4 / 8  (一个参数是32bit，4个字节, 8代表可以同时计算的输入通道数)*

### 计数器：

*copyWeightCnt ：C x M / COMPUTE_CHANNEL_IN_NUM*

*copyWeightTimes ：卷积核9个点*

*computeChannelOut ：可以同时计算的输出通道数*

### 加载流程：

将本层卷积的权重加载到 *9 x COMPUTE_CHANNEL_OUT_NUM* 个 *ram* 中，每个 *ram* 的 *addr* 最大为 *weightNum / COMPUTE_CHANNEL_OUT_NUM*。

加载权重数据的**单位**大小为 : *DATA_WIDTH x COMPUTE_CHANNEL_IN_NUM  bit*

##### 加载权重的顺序如下伪代码所示：

```c
addr = 0；
for(i = 0; i < 9; i += 1) // 卷积核9个点
	for(j = 0; j < weightNum; j += COMPUTE_CHANNEL_OUT_NUM ){ // 卷积核1个点对应的全部权重
		for(k = 0; k < COMPUTE_CHANNEL_OUT_NUM; k += 1){ // 可以同时计算的输出通道数
         	// 共例化了9 * COMPUTE_CHANNEL_OUT_NUM 个 weightNum / COMPUTE_CHANNEL_OUT_NUM 大小的ram, 共可以存放9 * weightNum个单位的权重
			ram[i][k](addr) = sData.payload; // 1次加载数据大小为DATA_WIDTH x COMPUTE_CHANNEL_IN_NUM bit
        }
		addr++;
	}
```

