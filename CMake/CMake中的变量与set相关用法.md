
需要预先学习：
- [[CMake基础]]

# 定义、修改普通变量

此处的普通变量指**变量仅在当前作用域有效**
```cmake
# 单个值的赋值
set(变量名 变量值)

# 多个值的赋值
set(变量名 值1 值2 值3 ...)    # 变量会变成一个列表，值之间用空格分隔
```

当我们需要使用变量的值的时候，可以通过 `${变量名}` 来引用，比如：
```cmake
# 单个值变量：定义项目版本号 
set(PROJECT_VERSION "1.0.0")   

# 多个值变量：定义需要编译的源文件列表 
set(SOURCE_FILES main.cpp utils.cpp core.cpp) 

# 引用变量的值 
message("项目版本：${PROJECT_VERSION}")     # message 用于在CMake配置时进行输出
message("源文件列表：${SOURCE_FILES}")
```

注： CMake的变量本质上都是字符串，其中列表在底层是用分号进行分隔，所以上述代码会有以下输出：
```
项目版本：1.0.0 
源文件列表：main.cpp;utils.cpp;core.cpp
```