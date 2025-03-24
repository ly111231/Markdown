查看当前的 swap 状态

```shell
swapon -s
```

增加一个临时的 swap 文件：创建一个 8GB 的 swap 文件，并将其启用

使用 `free -h` 再次检查是否启用了新的交换空间。

```shell
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

```shell
./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml ../TUM_dataset/rgbd_dataset_freiburg1_xyz/

```

