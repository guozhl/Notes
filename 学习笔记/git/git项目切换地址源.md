1. git commit -m "change."   # 先把所有为保存的修改打包为一个commit
2. git remote remove origin  # 删掉原来git源
3. git remoezhate add origin http://xxxxxxxxxx.git   # 将新源地址写入本地版本库配置文件
4. git push -u origin master   # 提交所有代码