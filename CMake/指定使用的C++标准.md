
需要预先学习：
- [[CMake中的变量与set相关用法]]
# 方法一：通过set函数给预设的宏赋值

CMake中有一个预设的宏 `CMAKE_CXX_STANDARD` ，通过对其赋值即可实现确定编译用的C++标准：
```cmake
# 增加标准 -std=c++ 11
set(CMAKE_CXX_STANDARD 11)
```

# 方法二：在执行cmake命令时用 `-D` 参数指定

可以在写入cmake命令的时候通过 `-D` 参数对对应的宏进行修改，如：
```cmake
cmake . -DCMAKE_CXX_STANDARD=11
```