## ORB-SLAM2复现


OS	ubuntu 20.04 LTS
Eigen	3.4.0
Pangolin	v0.6
g2o/DBoW2	included in repo,just ommitted.
OpenCV	3.4.16
编译过程中有两个问题, 分别是System.h中的 usleep()未声明以及LoopClosing.h中的std::map与分配器不匹配, 两个问题都能够在issue中找到解决方案.

https://github.com/raulmur/ORB_SLAM2/issues/954

https://github.com/raulmur/ORB_SLAM2/pull/585/commits/d5c04468ce85d600f8a0a23fa280b0153fe115e0

原文链接：https://blog.csdn.net/m0_57830768/article/details/137500035