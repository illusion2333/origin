# 虚拟机预编译文件和依赖
如果长安链无法启动, 或者启动时没有任何log输出, 可能是由于虚拟机所依赖的环境出现了问题, 请参照以下说明, 配置好环境

## Wamser
### 把下面文件放到系统环境变量PATH路径, 或者与chainmaker可执行程序的相同目录
- Windows: wasmer_runtime_c_api.dll
- Linux: libwasmer_runtime_c_api.so
- MacOS: libwasmer.dylib


## WXVM
### 安装gcc/g++编译工具链
- Windows: mingw64(下载链接https://chainmaker-wasm.oss-cn-beijing.aliyuncs.com/mingw64.zip)
- Linux: gcc/g++
    - CentOS: sudo yum install -y gcc.x86_64
- MacOS: gcc/g++(clang)

### 把预编译好的wxdec放到系统环境变量PATH路径
- Windows: prebuilt/win64/wxdec.exe
- Linux: prebuilt/linux/wxdec
- MacOS: prebuilt/macos/wxdec