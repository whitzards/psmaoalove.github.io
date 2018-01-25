````
#!/bin/sh -x -e

#参数１为bug的文件
bugTxt=$1

#sdk->tools->proguard->bin
SDK_Tools_Path='/home/xxxxx/Android/Sdk/tools/proguard/bin'

currentPath=$PWD
echo $currentPath
cd $SDK_Tools_Path

sed 's/.*at /at /' ${bugTxt} > after_format_bug.txt

./retrace.sh -verbose mapping.txt after_format_bug.txt

cd $currentPath
````
原理使用android sdk中的工具 retrace.sh ，脚本去替换了出错的*at  为at ,这样retrace 就可以识别了
