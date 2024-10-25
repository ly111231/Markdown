*channelIn：本层特征图输入通道数*

*channelOut：本层特征图输出通道数*

```c
// 以下伪代码不考虑图片、权重相乘后的通道、卷积核点数相加及后续量化等操作，只是图片、权重相乘的过程
for(row){
	for(col){
		// 以下伪代码不考虑行、列，即卷积核在图片任一处的卷积过程
		// 一次完整3*3卷积 -> 输出一个结果
		for(i = 0; i < channelOut; i += COMPUTE_CHANNEL_OUT_NUM){
			for(j = 0; j < channelIn; j += COMPUTE_CHANNEL_IN_NUM){
				// ConvCompute的一个计算单元，即FPGA同时可以计算这么多次乘法
				for(m = 0; m < COMPUTE_CHANNEL_OUT_NUM; m++){
					for(n = 0; n < COMPUTE_CHANNEL_IN_NUM; n++){
						// {3 * 3} 代表3*3卷积核的9个点，即9个点对应的权重和图片数据在ram中存放的相对位置相同，在这里省略具体位置，没有依次列出这9个点
						// weight(m) 代表COMPUTE_CHANNEL_OUT_NUM个权重分别同时与feature相乘
						{3 * 3} * feature(j + n) ** weight(m)( ⌊i / COMPUTE_CHANNEL_OUT_NUM⌋ * channelIn + j + n);
					}
				}
			}
		}
	}
}
```

