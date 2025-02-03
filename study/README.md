### 安装kitex工具与查看版本
```
$ go install github.com/cloudwego/kitex/tool/cmd/kitex@latest
$ kitex --version
v0.12.1
```

### 基础示例
#### 直接运行
```
1. 进入示例仓库的 hello 目录：
cd kitex-examples/hello

2. 运行服务端代码
go run .
// 输出类似日志代表运行成功
2024/01/18 20:35:08.857352 server.go:83: [Info] KITEX: server listen at addr=[::]:8888

3. 另启一个终端运行客户端代码
go run ./client

// 每隔一秒输出类似日志代表运行成功
2024/01/18 20:39:59 Response({Message:my request})
2024/01/18 20:40:00 Response({Message:my request})
2024/01/18 20:40:01 Response({Message:my request})

```

#### Docker 运行
```
1. 进入示例代码仓库：
$ cd kitex-examples

2. 构建docker镜像：
$ docker build -t kitex-examples .

3. 运行服务端代码：
$ docker run --network host kitex-examples ./hello-server
// 输出类似日志代表运行成功
2024/01/18 12:47:34.712415 server.go:83: [Info] KITEX: server listen at addr=[::]:8888

4. 运行客户端代码：
$ docker run --network host kitex-examples ./hello-client

// 每隔一秒输出类似日志代表运行成功
2024/01/18 12:48:20 Response({Message:my request})
2024/01/18 12:48:21 Response({Message:my request})
2024/01/18 12:48:22 Response({Message:my request})

```

#### 新增方法与协议
```
在hello.thift与handle.go添加协议与处理代码, 见git提交内容
```

#### 生成新代码
```
运行如下命令后，kitex 工具根据 hello.thrift 内容自动更新代码文件
kitex -module "github.com/cloudwego/kitex-examples" -service a.b.c hello.thrift

执行完上述命令后，kitex 工具将更新下述文件
更新 ./handler.go，在里面增加一个 Add 方法的基本实现
更新 ./kitex_gen， 里面生成有框架运行所必须的代码文件

```

#### 补全业务逻辑
```
上述步骤完成后，服务器代码的./handler.go中会自动补全一个Add方法的基本实现，类似如下代码：
// Add implements the HelloImpl interface.
func (s *HelloImpl) Add(ctx context.Context, req *api.AddRequest) (resp *api.AddResponse, err error) {
        .....
        // TODO: Your code here...
        // 新增业务代码
        resp = &api.AddResponse{Sum: req.First + req.Second}
        return
}

新增客户端调用: 
服务端已经有了 Add 方法的处理，现在让我们在客户端增加对 Add 方法的调用。
在 ./client/main.go 中你会看到类似如下的 for 循环：
for {
        .....
        // TODO: Your code here...
        // 新增业务代码
        addReq := &api.AddRequest{First: 512, Second: 512}
        addResp, err := client.Add(context.Background(), addReq)
        if err != nil {
                log.Fatal(err)
        }
        log.Println(addResp)
        time.Sleep(time.Second)
}
```

#### 重新运行
```
// 服务端
2024/01/18 21:07:43.638115 server.go:83: [Info] KITEX: server listen at addr=[::]:8888


// 客户端
2024/01/18 21:07:52 Response({Message:my request})
2024/01/18 21:07:53 AddResponse({Sum:1024})
2024/01/18 21:07:54 Response({Message:my request})
2024/01/18 21:07:55 AddResponse({Sum:1024})
2024/01/18 21:07:56 Response({Message:my request})
2024/01/18 21:07:57 AddResponse({Sum:1024})
```


