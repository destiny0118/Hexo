# linux

## bug

1. Linux执行.sh文件，提示No such file or directory的问题的解决方法

   ```shell
   #fileformat格式错误
   vim打开sh文件
   :set ff(fileformat=dos)
   :set ff=unix
   :wq
   ```

   

2. Command 'clang' not found, but can be installed with

   [Fixing 'clang' and 'clang++' not found after installing 'clang-3.5' package | DeviceTests](https://devicetests.com/fixing-clang-and-clang-not-found#:~:text=Open a terminal and run the following command%3A,the repository%2C and installs it on your system.)



# Cmake
## 设置cmake编译器
[CMake I 获取/设置编译器_cmake_cxx_compiler_id-CSDN博客](https://blog.csdn.net/weixin_39766005/article/details/122435780)