````
1.vim changeJdkVersion.sh
````
2.写入下面的命令
# Ubuntu14.04 LTS 开发环境Android源码编译，jdk切换方法
````
sudo update-alternatives --config jar
sudo update-alternatives --config jarsigner
sudo update-alternatives --config java
sudo update-alternatives --config javac
sudo update-alternatives --config javadoc
sudo update-alternatives --config javah
sudo update-alternatives --config javap
echo "******** 配置JDK切换工具成功！********"
````
