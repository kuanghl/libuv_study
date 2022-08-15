# libuv 在 ubuntu, vscode 中的 debug

前阵子看来 moduo 的源码以及陈硕的 `Linux多线程服务端编程：使用muduo C++网络库`, 收获很多. 最近一直在看 libuv 的相关源码, 起初只看代码, 后来遇到有些地方只看代码感觉很疑惑, 于是尝试进行相关 debug 调试, 环境是 `ubuntu`, `vscode`. 整个构建过程中, 唯一比较难受的是: 自己之前一直在 fork 的 libuv 的仓库中进行相关注释, 可能不小心影响到了源码, 导致编译出来的动态库运行就会报错. 后来反思之后拿干净的库来生成 debug 动态库, 基本完全 ok.

![error](https://shubuzuo.aiur.site/pic_1660501540241-20.png)  

## 环境准备

### ubuntu

这是我个人的操作系统环境，读者可能是 windows 或者 mac, 其他.

### vscode

个人习惯在 vscode 上编写 C++, C, C#, TS, Golang. 使用起来还挺不错.

### cmake

libuv 支持 cmake 和 automake 来生成相关 libuv 相关文件, 包括静态库, 动态库等. 需要安装 [cmake](https://cmake.org/download/).

## debug

### 生成 libuv 的 debug 动态库

> 并不是所有的动态库都支持 debug, 只有在生成动态库时指定了 debug 才能生成 debug 动态库. 

因为 libuv 的 CMakeLists.txt 并没有指定 debug, 所以需要在 CMakeLists.txt 末尾添加, 来生成 libuv 的 debug 动态库.

```cmake
set(CMAKE_BUILD_TYPE "Debug")
```

这样方便进行 debug.

vscode 中安装 cmake 插件, 然后将 libuv 添加到工作目录的 CMakeLists.txt 中, 方便 cmake 插件的扫描.

```cmake
add_subdirectory(./libuv)
```

之后点击 `libuv.so` 的下载图标, 生成 libuv 的 debug 动态库.

![vscode-cmake-build-libuv.so](https://shubuzuo.aiur.site/pic_1660500748131-45.png)  

之后就会看到 build 目录中有 libuv 下面会有 `libuv.so`. 这意味着生成成功.

### vscode 调试 cmake 的 c++

launch.json

```jsonc
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C/C++",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/bin/${fileBasenameNoExtension}", // 最后执行的程序
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "preLaunchTask": "compile", // 前置任务, 生成可执行文件
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

tasks.json

```jsonc
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "compile",
            "command": "cmake",
            "args": [   // cmake build 的相关参数
                "--build",
                "${workspaceFolder}/build", // build 目录
                "--config",
                "Debug",
                "--target",
                "${fileBasenameNoExtension}", // 生成的可执行文件
                "-j",
                "10",
                "--"
            ],
            "problemMatcher": {
                "owner": "cpp",
                "fileLocation": [
                    "relative",
                    "${workspaceRoot}"
                ],
                "pattern": {
                    "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                }
            },
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

### f5 调试

打开 `src/main.c`, 然后按 `F5` 即可调试.

![debug-main](https://shubuzuo.aiur.site/pic_1660502929531-11.png)  

![debug-uv-run](https://shubuzuo.aiur.site/pic_1660503012908-41.png)  

