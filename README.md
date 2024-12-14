# Jester
一款基于 控制台指令操作 的 NUGET 发布工具。

## 工程约束

### 项目结构
1. 单项目，sln 与 csproj 同目录，工具将把该项目作为 “合法” 项目。
2. 多项目，解决方案中，有 src 文件夹，工具将把 src 文件夹里的 `c#` 项目作为 “合法” 项目。

### csproj 文件结构
参与打包与发布的项目工程文件应该遵守以下几点：
1. PropertyGroup 下有打包相关的信息。工具仅负责 [更新] 或 [生成] `<Version>/<AssemblyVersion>/<FileVersion>` 三个版本节点.
2. 在打包(pack)命令执行过程中，若未指定 `--no-combine` 命令开关，targets 文件将会被合并输出，工具将清除 `<None Include="targets\...` 节点，再生成新节点。

### targets 文件
所有 targets 文件都需要剔除 `<project>` 根节点, 工具将自动合并所有文件内容到同一 `<project>` 节点下。

## 工具使用

### 界面与输入端

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212013299-377158665.png)

### 命令支持

输入命令 {?} 可以查看改工具支持的命令。
![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212103736-121960685.png)

### Nuget Key 操作
![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209212705438-87308925.png)

```bash
#查看当前 nuget key 列表
nl

#添加一个 nuget key 发布方案
na publishTest oy2nabcdefghiojxv http://公司私有的nuget地址
#不指定 source 使用 nuget 官方的地址
na publishTest oy2nabcdefghiojxv 

#删除发布方案
nd publishTest

```
### Solution 操作

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209220145548-21667874.png)

该工具以 sln 解决方案为单位，支持添加、删除、更新 NUGET KEY 等操作。

以下为命令使用案例：

```bash
#查看当前存在的解决方案
sl 

#增加一个解决方案
sa c://mysolution

#删除列表中第一个方案
sd 0

#让该方案在发布时使用某个 NUGET KEY
#锁定第一个方案
sc 0

```

### 检查与打包
该工具以 CHANGELOG.MD 文件为打包发版依据。

```bash
#查看当前解决方案列表
sl

#锁定你要操作的解决方案
#锁定列表中第一个解决方案 
#若没有 CHANGELOG 文件工具将自动生成一个文件
sc 0

#按照文件中的案例格式编辑被打开的 CHANGELOG 文件

#使用 pl 查看当前 CHANGELOG 文件与当前解决方案的匹配信息
pl



#确认无误，打包
pack

#若不发布隐式 using 文件，则
pack --no-using

#若不合并 targets 文件，则
pack --no-combine

#若有多个 targets 文件，则
#1. 去掉文件中的 <project></project> 节点标签，以便 Jester 合并内容到同一个 <project> 节点下。
#2. 不要使用 Jester.Usings.targets 来命名文件，该文件预留给了 Jester 输出隐式命名空间用。
#3. 不要使用 Jester.Combine.targets 来命名文件，该文件预留给了 Jester 合并所有 targets 文件的输出文件使用。
```

> pl 命令会自动拉去 nuget 官方记录的版本信息，local 为本地工程文件中的版本信息，next 为 CHANGELOG 被打包的版本信息

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209194606661-1235031892.png)

### 发布

```bash
#如果之前已经锁定解决方案则不用重新锁定了
#若被锁定的解决方案没有关联发布策略
su publishTest

#查看打包好的包 在 your_sln_folder/nugets 文件夹中

#发布
push
```

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209210350097-90044594.png)

![](https://img2024.cnblogs.com/blog/1119790/202412/1119790-20241209195226912-1947652434.png)

