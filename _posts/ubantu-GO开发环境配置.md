一、之前使用go写了下脚本把配置记录下
二.配置go环境
````
Ubuntu安装Go：
sudo add-apt-repository ppa:gophers/go
sudo apt-get update
sudo apt-get install golang-stable
````
或
````
sudo apt-get install golang
````
或者直接下载go语言安装包

下载地址：http://www.golangtc.com/download 如果下载不下来请科学上网

三.解压
````
$ tar -C /usr/local -zxf go1.7.4.linux-amd64.tar.gz
````
四.打开HOME目录下的.bashrc文件
````
         vim ~/.bashrc
````
五.在文件的最后添加以下内容
````
    export GOROOT=/usr/local/go
    export PATH=$PATH:$GOROOT/bin
    export GOPATH=/home/xxxx/goworkspace     
    # 注明：这是项目路径
````
六、保存设置并运行命令使其生效
````
    source ~/.bashrc
````
OK！Go语言的开发环境已经安装成功了！一、此脚本用go来编写，使用前配置go环境
