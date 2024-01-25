## forge geiger

### 名称

forge-geiger - 检测在 foundry 项目及其依赖项中使用不安全的作弊代码。

### 概要

``forge geiger`` [*options*] [*path*]

### 描述

检测在 foundry 项目及其依赖项中使用不安全的作弊代码。

### 选项

`--root` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;项目的根路径。默认情况下，这是当前 git 存储库的根目录，或者当前工作目录。

`--check`      
&nbsp;&nbsp;&nbsp;&nbsp;以 “检查” 模式运行。如果未发现不安全的作弊代码，则退出并返回 0。如果检测到不安全的作弊代码，则退出并返回 1。

`--full`  
&nbsp;&nbsp;&nbsp;&nbsp;即使未发现不安全的函数，也打印所有文件的完整报告。

{{#include common-options.md}}

### 参见

[forge](./forge.md) 