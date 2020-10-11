## 基础命令
* 新建文件夹
  ```bash
  mkdir Shell
  ```
* 在某个已存在目录下新建文件
  ```bash
  touch Shell/shell.sh
  ```
* 在当前文件夹下新建文件
  ```bash
  touch shell.sh
  ```
* 编辑某个文件
  ```bash
  vi shell.sh
  # i 可进入编辑状态
  # esc 退出编辑状态
  # :wq 保存并离开
  ```
* 查看文件内容
  ```bash
  cat shell.sh
  ```
* 运行 shell 脚本
  ```bash
  # 作为可执行程序   
  chmod +x ./shell.sh  # 使脚本具有执行权限  
  ./shell.sh  # 执行脚本

  # 作为解释器参数
  /bin/sh shell.sh
  ```




