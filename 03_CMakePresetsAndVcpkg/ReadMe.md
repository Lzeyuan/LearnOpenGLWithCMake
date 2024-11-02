# CMakePresets和Vcpkg

> 仍然是分而治之

# 1 CMakePresets
CMakePresets这个东西前几个项目就有了，现在终于写到他了。
## 1.1 作用
还是那句话，转移复杂度，分而治之。

便于用户指定通用的**配置、生成和测试**选项，并与**他人共享**。

目前只用到了配置和生成。

## 1.2 CMakePresets和CMakeUserPresets

- `CMakeLists.txt`：写死的配置
- `CMakePreset.json`： 加入版本控制，配置宏之类的参数

- `CMakeUserPreset.json`： 不加入版本控制，配置本地开发环境参数

为了演示，这一节项目上传了 `CMakeUserPresets.json` ，记得修改 `CMAKE_TOOLCHAIN_FILE` 。

**VSCode的CMake插件，并不会读取系统环境**，也就是说 `$ENV{VCPKG_ROOT}` 是空值。

**比如：**
- `CMAKE_POLICY_DEFAULT_CMP0091`这个规则和Windows平台相关的就写在`CMakeUserPresets.json`里
- 生成目录路径、安装目录路径一般约定熟成写在`CMakePresets.json`里
- `$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake` vcpkg目录是相对文件路径，写在`CMakePreset.json`里

具体查看文件，都是KV值很好懂。

## 1.3 BuildPresets
```
{
  "name": "debug-vcpkg-build",              构建配置名字
  "displayName": "x64-vcpkg-debug",         gui选项名字
  "configurePreset": "x64-vcpkg-debug",     对应上面写的配置
  "configuration": "Debug",                 和"CMAKE_BUILD_TYPE": "Debug"冲突
  "verbose": true                           VS下CMake设置verbose无用，要在这里设置
},
```

# 2 Vcpkg
不依赖cmake版本，因为他是作为工具链传入cmake。
## 2.1 vcpkg.json管理独立环境
和python的conda一样，每个项目都可以创建专属的环境安装特定版本的包，不和其它环境冲突。

[教程：从清单文件安装依赖项](https://learn.microsoft.com/zh-cn/vcpkg/consume/manifest-mode?tabs=msbuild%2Cbuild-MSBuild)

- 生成vcpkg.json：`vcpkg new --application`

- 添加库：`vcpkg add port [库]`

- 安装：`vcpkg install`，CMake配置时会自动安装

必须要使用`overrides`才能指定版本号。
> 不要指定版本号，详见吐槽。

`builtin-baseline`，现在在 `vcpkg-configuration.json` 里。
```json
{
  "name": "cmake-learn",
  "version-semver": "0.0.1",
  "dependencies": [
    {
      "name": "glfw3",
      "platform": "(windows & x64)"
    },
    {
      "name": "assimp",
      "version>=": "5.3.1",
      "platform": "(windows & x64)"
    }
  ],
  "overrides": [
    {
      "name": "glfw3",
      "version": "3.3.9"
    }
  ],
  "builtin-baseline": "7032c5759f823fc8699a5c37d5f7072464ccb9a8"
}
```

## 2.2 指定平台

[平台表达式](https://learn.microsoft.com/zh-cn/vcpkg/reference/vcpkg-json#platform-expression)

很坑，这个选项意思是：`满足这个条件才安装`，具体安装配置参考：[三元组参考](https://learn.microsoft.com/zh-cn/vcpkg/users/triplets)。

比如：`"platform": "(windows & x64 & static)"`，但是没有配置[
VCPKG_TARGET_TRIPLET](https://learn.microsoft.com/zh-cn/vcpkg/users/buildsystems/cmake-integration#vcpkg_target_triplet)为`x64-windows-static`他不会安装到项目环境里，因为默认是`x64-windows`。

**甚至不写都行**，直接看`VCPKG_TARGET_TRIPLET`设置。
例如：`set(VCPKG_TARGET_TRIPLET x64-windows)`

## 2.2 吐槽
和构建构建工具一样C++没几个工具是用的舒服的。

每个库都会有版本号更新，而对于vcpkg，某个版本号下修bug的提交（不改版本号），会视为一次`port-version`记录起来。

这就导致一个坑，在使用**指定版本**的时候，比如某个库在3.3.9版本下更新了10次，就是说会有0~9的`port-version`，你指定3.3.9版本，他不会去安装最新的修复bug那一次提交（3.3.9#10），而是安装第一次发布时的提交，即3.3.9#0，而这种情况一般就是想要最新的更新。

本来是有选项显示某个库某个版本的`port-version`，但是删除了[Remove command x-history](https://github.com/microsoft/vcpkg-tool/pull/689)
那怎么解决呢？查看 `vcpkg安装目录下/version/安装库名开头字母-/安装库名.json`，里面就有对应`port-version`记录了。