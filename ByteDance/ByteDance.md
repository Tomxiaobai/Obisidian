## 开发环境配置
#### 方式一 #开发环境配置 （推荐）
1. 搭建MAC本地开发环境
2. GoLand搭建CloudDev环境 配置BOE环境
3. 注意，这里BOE可以理解为是测试环境，所有代码的测试、联调、运行、Debug都是在BOE环境中进行
4. 
#### 方式二 
1. 远程开发机配置开发
2. VsCode链接开发机进行开发

### GoLand拉取代码仓库失败

1. fatal: Could not read from remote repository.


## Kitex

- 常用命令
```Bash
cd $EXAMPLE_PATH

kitex -module code.byted.org/your_name/kitex_example \
    -service kitex.thrift.example idl/kitex_greet.thrift

# this is necessary since v0.14.0+ is not compatible with Kitex
# Refer to doc "not enough arguments" error for more information
go mod edit -replace=github.com/apache/thrift=github.com/apache/thrift@v0.13.0

go mod tidy
```
>kitex 默认会将生成代码生成在当前目录（也就是 $EXAMPLE_PATH）。这里简单说明一下参数的含义：
>
>`-module`参数应和 `go.mod` 文件中的 module name 相同，在本例中为 code.byted.org/your_name/kitex_example
>
>常用的module参数为  code.byted.org/sysmodulename/func 举例： 
>
>kitex -module code.byted.org/game/tmtest \\n -service webcast.game.tmtest 
>
>~/go/src/code.byted.org/webcast/rpc_idl/webcast/test/tm_test.thrift

>- `-service`参数表明需要生成服务端代码，建议指定为服务端的`PSM`（[什么是"PSM"](https://bytedance.larkoffice.com/wiki/wikcn2ZAtBJMFXuZi70ftWovxgc?bk_entity_id=enterprise_129)），在本例中为 `kitex.thrift.example`；
>- IDL 文件**必须是最后一个参数**，在本例中为 `idl/kitex_greet.thrift`。



## 工具相关&网站
- [CloudIDE](https://cloud.bytedance.net/cloudide/environments?x-resource-account=public)
- [CloudDEV](https://bits.bytedance.net/clouddev/interface_test?address=&api=GetOperationLogs&cluster=default&env=prod&form_type=RPC&http_method=GET&idc=boe&idl_source=1&idl_version=feat_newbee_tongmeng&protocol=https%3A%2F%2F&psm=webcast.newbee.core&test_plane=2&zone=BOE)
- [直播PB](https://code.byted.org/webcast/rpc_idl)

## 开发问题汇总
1. 客户端如何寻址到后台结构
2. 后台上线流程
3. 监控告警系统
4. Janux 前端交互 [前端协议转换](https://cloud.bytedance.net/bam/rd/webcast.gamecp.promote/idl?x-resource-account=public&cluster=default&version=1.0.1142)

## 开发流程
1. 后台更新协议(webcast_idl) $\rightarrow$ overpass根据个人分支生成代码([overpass](https://overpass.bytedance.net/idl_info?s=webcast.newbee.core)) $\rightarrow$ [bam导入IDL](https://cloud.bytedance.net/bam/rd/webcast.game.tmtest/api_doc/new_api_doc?x-resource-account=public)
2. GoLand可以搭建BOE环境测试接口