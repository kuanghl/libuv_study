# 运行

```bash
git pull
git submodule update --init
```

## build libuv

在 libuv ./libuv/CMakeLists.txt 末尾添加, 来生成 libuv 的 debug 动态库

```cmake
set(CMAKE_BUILD_TYPE "Debug")
```

vscode 安装 cmake 插件, 点击 build, 生成 libuv 的动态链接库.

```
mkdir build 
cd build
cmake ..
make -j16
make install
```


## debug main

打开 main.c, f5 直接运行, 需要安装 cmake, 否则提前构建会过不去. 提前构建使用 cmake 来完成, 最终生成可执行文件.

**请注意 vscode 下 debug 相关文件中, 默认平台为 Ubuntu, 使用 gcc 构建**.
