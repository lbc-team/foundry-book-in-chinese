#### Project Options

`--build-info`  
&nbsp;&nbsp;&nbsp;&nbsp;生成构建信息文件。

`--build-info-path` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;输出目录的路径，构建信息文件将被写入。

`--root` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;项目的根路径。默认情况下，这是当前 git 仓库的根目录，或当前工作目录。

`-C` *path*  
`--contracts` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;合约源代码目录。
&nbsp;&nbsp;&nbsp;&nbsp;环境变量：`DAPP_SRC`

`--lib-paths` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;库的文件夹路径。

`-R` *remappings*  
`--remappings` *remappings*  
&nbsp;&nbsp;&nbsp;&nbsp;项目的重映射。

&nbsp;&nbsp;&nbsp;&nbsp;该参数是一个逗号分隔的重映射列表，格式为 `<source>=<dest>`。

`--cache-path` *path*  
&nbsp;&nbsp;&nbsp;&nbsp;编译器缓存的路径。

`--config-path` *file*  
&nbsp;&nbsp;&nbsp;&nbsp;配置文件的路径。

`--hh`  
`--hardhat`  
&nbsp;&nbsp;&nbsp;&nbsp;这是一个方便的标志，与传递 `--contracts contracts --lib-paths node-modules` 相同。
