### 语法
```bash
# 等号左右不可有空格
my_name="skye"
echo ${my_name}

# 只读变量不可修改和删除
readonly my_name

# 删除变量
unset my_name   
```

###  Shell 字符串
```bash
# 单引号

# 单引号字符串的限制：   
# 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的
# 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用

str='this is a string'


# 双引号

# 双引号的优点：
# 双引号里可以有变量
# 双引号里可以出现转义字符
my_name='skye'
str="Hello, I know you are \"$my_name\"! \n"
echo -e $str
# 输出 Hello, I know you are "skye"! 

# 获取字符串长度
string="skye"
echo ${#string}
# 输出 4

# 提取子字符串
string="BIBI is a great singer"
echo ${string:1:4}
# 输出 IBI

# 查找子字符串
# 查找字符 i 或 o 的位置(哪个字母先出现就计算哪个):
string="BIBI is a great singer"
echo `expr index "$string" is`
# 输出 6
```

### Shell 数组
bash支持一维数组（不支持多维数组），并且没有限定数组的大小。
```bash
array=(1 2 3 4)
array[5]=5

# 读取数组
a=${array[5]}
echo $a
# 输出 5

# 使用 @ 符号可以获取数组中的所有元素
echo ${array[@]}
# 输出 1 2 3 4 5

# 获取数组的长度
# 取得数组元素的个数
length=${#array[@]}
# 或者
length=${#array[*]}
# 取得数组单个元素的长度
lengthn=${#array[n]}
echo length
# 输出 5

echo lengthn
# 输出 1
```
