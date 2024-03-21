###  PProf工具总结
	pprof 是 go 自带的一个对程序的性能进行可视化和分析的工具，它从 profile.proto 格式的采样数据集中读取数据，并生成可视化报告，帮助我们分析我们的程序性能，进而有所针对进行优化。
	当服务在运行时，有时候会遇到内存占用越来越大、将机器的CPU占满、goroutine数量暴涨的情况，或者当我们准备针对已有的代码进行运行效率、内存占用等方面进行优化，这时候就是 pprof 可以派上用场的时候了。

#### 使用方式
##### net/http/pprof 端口监听

> 该方式适用于服务型应用，通过添加监听端口，采集HTTP Server运行时的数据进行分析
###### 生成报告进行分析
- 在引入了_ net/http/pprof包后，在程序运行期间可以浏览访问网址 http://localhost:8080/debug/pprof 打开后可以看到以下文件
  ![[pprofile one.png]]
- 可以看到一些分析指标的数量以及链接，可以点击链接查看详情，这些分析指标对应的含义是：
	- allocs：查看过去所有的内存分配信息
	- block：查看导致阻塞同步的堆栈跟踪
	- cmdline：当前程序命令行完整的程序调用
	- goroutine：查看当前所有运行的goroutines 堆栈跟踪
	- heap：查看活动对象的内存分配情况
	- mutex：查看导致互斥锁的竞争持有者堆栈跟踪
	- profile：进行CPU分析，可以通过GET请求参数指定时间范围
	- threadcreate：查看创建新操作系统线程的堆栈跟踪
	- trace：生成对目前程序执行的分析文件，用于通过go tool trace进行分析，可以通过GET指定时间范围
###### 终端命令行分析
- cpu profile 分析 go tool pprof http://localhost:8080/debug/pprof/profile 默认采集30s数据，所以执行后需要等待30s后才可以进入命令行交互模式，可以再请求地址设置参数seconds=60,表示采集60s或者其他时长的数据进行分析。
  ![[cpu pprof.png]]
- heap profile go tool pprof http://localhost:8080/debug/pprof/heap 分析内存使用情况
  ![[heap pprof.png]]
- goroutine profile go tool pprof http://localhost:8080/debug/pprof/goroutine 分析程序协程情况 使用trace打印所有堆栈信息
  ![[goroutine pprof.png]]
###### 可视化界面分析

- `wget http://localhost:port/debug/pprof/profile`
- `curl -o profile http://localhost:port/debug/pprof/profile`
- 在生成proflie后指定一个端口分析profile，这个端口和上述程序执行的端口不一样即可。
  `go tool pprof -http=:8081 profile`
- 后续可以看如下的火焰图分析
  ![[fire view pprof.png]]

- 也可以细致分析详细的情况，通过点击网页的view选择
  ![[fire view 参数分析.png]]
  
##### runtime/pprof 生成pprof文件

> 该方式适用于工具性应用，在执行的代码中间通过使用runtime/pprof包，生成一个pprof文件，然后对该文件以命令行浏览器等形式进行数据分析。

- 示例代码：对一个字符串循环追加内容1k次
```go
impot(
	"runtime/pprof"
)

func main() {
	cpu_pprof, _ := os.Create("cpu.pprof")
	_ = pprof.StartCPUProfile(cpu_pprof)
	// do sth
	pprof.StopCPUProfile()
	_ = file.Close()
	_ = cpu_pprof.Close()
}
```
- 内存分析是记录某个时刻的内存信息，所以直接创建一个pprof文件并写入然后关闭：
```go
impot(
	"runtime/pprof"
)

func main() {
	// do sth
	runtime.GC()
	heap_pprof, _ := os.Create("heap.pprof")
	_ = pprof.WriteHeapProfile(heap_pprof)
	_ = heap_pprof.Closr()
}
```
###### 终端命令行分析
 `go tool pprof cpu.pprof`
###### 可视化界面分析
`go tool pprof -http=:8081 cpu.pprof`



