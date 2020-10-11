### 语法
```bash
. filename   # 点号(.)和文件名中间有一空格
或
source filename
```

### 实例
创建两个 shell 脚本文件。 

test1.sh 代码如下：
```bash
#!/bin/bash
my_name="skye"
```

test2.sh 代码如下：
```bash
#!/bin/bash
. ./test1.sh
# source ./test1.sh

echo "my name is ${my_name}"
# my name is skye
```
