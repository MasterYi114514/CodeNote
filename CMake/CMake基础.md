
**必要的文件名称**： `CMakeLists.txt`

# 基础内容

```cmake
# 请求CMake的最小版本
cmake_minimum_required(VERSION 4.1.0)

# 指定项目名称
project(test)

# 生成可执行程序
# 这里的直接写 main.cpp 默认为和 CMakeLists 在同一目录
# 在其他目录的需要用相对路径（相对于CMakeLists.txt）
# hahaha 是生成的可执行文件的名称
add_executable(hahaha main.cpp)  

# 也可以加入多个可执行程序
# add_executable(hahaha main.cpp a.cpp b.cpp c.cpp)
```

注：
1. 如果需要在 `add_executable` 中自动查找某格式的文件请参考[[搜索文件]]
2. CMake中用到的文件路径等均可加双引号也可不加

# 创建命令

在命令行键入
```bash
cd "CMakeLists的目录"  # 比如路径为"D:\test\CMakeLists.txt",就 cd D:\test

# 这里的“.”表示CMakeLists.txt所在的目录
# 这个“.”也可以省略，默认为当前目录
# 执行此条命令后会生成 "D:\test\build"
cmake -B build . 

# 也可以用-S参数来显式指定原码目录，如：
# cmake -S. -B balabala
# cmake -SD:\test -B vivovivo

# 也可以用-G参数指定工具链，默认为MSVC
# cmake -B build -G"MinGW Makefiles" .   # 指定MinGW的工具链

# 也可以用-D参数定义修改缓存变量，格式为： -D<变量名>=<值>
# 如：
# cmake .. -DCMAKE_BUILD_TYPE=Release
```

到这一步为止只是生成了指定了构建目录，并生成构建文件。

接下来，我们需要用 `--build` 参数来实现编译

```bash
# 在 -B 命令执行后
cmake --build build  # 后一个build是 -B 命令生成的构建文件
```

最后，会在 build 目录里面生成对应的可执行文件 `hahaha.exe` ，可以直接运行

```bash
build/hahaha
```