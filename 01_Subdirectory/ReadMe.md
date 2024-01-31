# 子目录（模块）
> 复杂度不会消失，但是可以转移，分而治之

# include和add_subdirectory

include等价于`#include`

stackoverflow的QA：[cmake: add_subdirectory() vs include()](https://stackoverflow.com/questions/48509911/cmake-add-subdirectory-vs-include)

主要区别：
- 使用add_subdirectory会把目录结构体现在生成目录里
- 使用add_subdirectory会创建一个新作用域

子项目和正常项目差不多，不过要考虑共享信息给引用项目。

```cmake
# glad子项目
project(glad)

# 默认STATIC，可不写
add_library(
    ${PROJECT_NAME}
    STATIC
    src/glad.c
)

# 这里把头文件 PUBLIC 共享给引用的项目
target_include_directories(
    ${PROJECT_NAME}
    PUBLIC
    include
)
```

如果是纯动态库项目，应该怎么封装呢？请看下一节。