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
5. BOE部署了服务 但是在测试接口时报错： 
	找不到服务地址，可能的原因: 1.psm不存在; 2.psm未注册consul (sd lookup {service_name} 为空), 可能是tce服务没有配置监听端口; 3.检查是否cluster/env 信息有误。 如果是在测试，可以尝试输入自定义地址 -- 已解决
6. 部分业务场景&名词不太熟悉 -- 待沟通
7. 

## 开发流程 & 规范
1. 后台更新协议(webcast_idl) $\rightarrow$ overpass根据个人分支生成代码([overpass](https://overpass.bytedance.net/idl_info?s=webcast.newbee.core)) $\rightarrow$ [bam导入IDL](https://cloud.bytedance.net/bam/rd/webcast.game.tmtest/api_doc/new_api_doc?x-resource-account=public)
2. GoLand可以搭建BOE环境测试接口
3. 使用Jsonx 并且 尽量函数要写通用
4. CR流程标准：
	1. 如果定义了新接口，接口定义完需要提交一次CR
	2. 完成接口或者函数实现，如果新增代码过多 需要拆分进行CR
	3. 完成BUG修复需要CR


## 本地开发相关文件
- Navicate root TM.123


## 组件学习归纳
### AnyCache
	
### Golang源码解析 [[Language/GoLang|相关总结]]
#### Slice 
##### Go Slice 源码分析

Go语言的slice结构体定义在`runtime/slice.go`文件中，其核心结构如下：

```go
type slice struct {
    array unsafe.Pointer // 指向底层数组的指针
    len   int             // slice的长度
    cap   int             // slice的容量
}
```

slice的创建通常通过`make`函数或者直接通过数组的切片操作来完成。`make`函数的源码实现如下：

```go
func makeslice(et *_type, len, cap int) unsafe.Pointer {
    // ...内存分配和初始化操作...
    return mallocgc(mem, et, true)
}
```

slice扩容的机制在`growslice`函数中实现，当slice的容量不足以添加新元素时，会触发扩容操作：

```go
func growslice(et *_type, old slice, cap int) slice {
    // ...扩容逻辑，包括计算新容量、内存分配和数据拷贝...
    return slice{p, old.len, newcap}
}
```

##### 常见用法

1. **创建Slice**：
   ```go
   // 使用make函数创建一个长度为0，容量为3的int类型的slice
   slice := make([]int, 0, 3)
   ```

2. **追加元素**：
   ```go
   // 向slice中追加元素
   slice = append(slice, 1, 2, 3)
   ```

3. **切片操作**：
   ```go
   // 获取slice的一个子序列
   subSlice := slice[1:4] // 从第二个元素开始到第四个元素
   ```

4. **遍历Slice**：
   ```go
   // 遍历slice中的每个元素
   for i := range slice {
       fmt.Println(slice[i])
   }
   ```

5. **修改Slice元素**：
   ```go
   // 修改slice中的元素
   slice[0] = 10
   ```

##### 可能出现的错误用法

1. **超出Slice长度访问**：
   ```go
   // 错误的访问方式，可能会导致panic
   value := slice[10] // slice的长度小于11
   ```

2. **修改从函数返回的Slice**：
   ```go
   func f(s []int) {
       s[0] = 10 // 这只会修改传入的slice的副本，不会影响原始slice
   }
   ```

3. **忘记Slice扩容可能导致的性能问题**：
   ```go
   // 频繁的append操作，没有预先分配足够的容量，会导致多次扩容
   slice := make([]int, 0, 1)
   for i := 0; i < 1000; i++ {
       slice = append(slice, i)
   }
   ```

4. **错误的容量判断**：
   ```go
   // 错误的判断方式，cap是slice的总容量，不是剩余空间
   if cap(slice) == 0 {
       // 错误，这会判断slice是否已经扩容到其最大容量
   }
   ```
**注意事项** [[Language/GoLang#^727187|注意边界处拷贝]]
- Map
- sync.Map
- 


### GoLang编码规范[[Language/GoLang|GoLang]]
### Kafka

### RocketMQ

