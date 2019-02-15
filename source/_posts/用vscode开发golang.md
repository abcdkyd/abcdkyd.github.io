### 首先配置GOPATH

`GOPATH`允许多个目录，当有多个目录时，请注意分隔符，多个目录的时候Windows是分号`;`，Linux系统是冒号`:`，当有多个GOPATH时默认将`go get`获取的包存放在第一个目录下，在开发的gopath中写代码，使用go工具(`go install`, `go build`) 会将二进制包安装到开发的gopath中

`GOPATH`目录约定有三个子目录

- `src`存放源代码(比如：.go .c .h .s等)
- `pkg`编译时生成的中间文件（比如：.a）
- `bin`编译后生成的可执行文件（为了方便，可以把此目录加入到 `$PATH` 变量中，如果有多个gopath，那么使用`${GOPATH//://bin:}/bin`添加所有的bin目录）


### 使用vscode

在官网下载vscode安装，然后安装Microsoft的go插件，由于网络原因，这时会提示你现在有好几个插件安装失败。

手动把插件安装上：

1. 在`$GOPATH`的第一个目录下，建立目录：`mkdir -p src/golang.org/x`
2. 进入上面新建的目录中，下载插件源码：`git clone https://github.com/golang/tools.git tools`
3. 返回`$GOPATH`的第一个目录，执行

	```
	go install github.com/ramya-rao-a/go-outline
	go install github.com/acroca/go-symbols
	go install golang.org/x/tools/cmd/guru
	go install golang.org/x/tools/cmd/gorename
	go install github.com/rogpeppe/godef
	go install github.com/sqs/goreturns
	```
4. 单独安装golint，进入目录`$GOPATH/src/golang.org/x`，下载golint源码：`git clone https://github.com/golang/lint`
5. 返回`$GOPATH`的第一个目录，执行`go install golang.org/x/lint/golint`
6. 最后在vscode的setting.json中添加`"go.formatTool": "goreturns"`