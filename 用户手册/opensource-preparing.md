## 1 注释

### 文件头注释

<u>结论：增加license说明，内部确定使用GPL，需要产品确定</u>

find . -path "./pb" -prune -o -type f -name "*.go"|grep -v "./pb" |xargs -L1 ./add_file_header.sh

Add_file_header.sh: 

```sh
#/bin/sh
cat gpl.txt $1 > $1.bak.bak.bak.bak
mv $1.bak.bak.bak.bak $1
```



### 函数注释

<u>结论：模块对外暴露的接口要有注释，如果有对应的interface，写在interface里，实现的地方引用，并补充特殊的地方（如有）。如果没有interface，直接写在函数上。</u>

### 函数关键逻辑注释

<u>结论：不做引制要求</u>

## 2 项目ReadMe写上license

<u>结论：</u>

- <u>ReadMe参考bitxhub标准化。</u>

- <u>模块readme：功能介绍、接口、配置项（全）、模块设计、依赖（包版本）</u>

## 3 项目license文件

<u>结论：根据项目使用的license协议附带一个license文件</u>

## 4 包引用路径

<u>结论：后期确定使用哪个开源平台后再修改</u>

## 5 日志标准化

<u>结论：</u>

- <u>日志格式使用原来定义的格式</u>

- <u>脚本+人工方式过滤现有日志输出的代码，找出不标准的日志，推进修改</u>

### 6 模块接口

单独确认后推进修改

