# 格式化U盘
```
1. 将U盘格式化为 FAT32 格式
2. 在U盘根目录下创建空白文件“phonehome.phy”
3. 每个 SC 控制器上插入一个 U盘
```
# 收集日志
```
1. DSM 上选择相应的 SC 存储，鼠标点击右上角的 “编辑 SC 设置 ”，鼠标点击
“SupportAssist”
2. 在“SupportAssist”下，鼠标点击关闭 PhoneHome
3. 在“SupportAssist”下，鼠标点击 “向 USB 发送 SupportAssist 信息”。日志开始写入 U盘
4. 完成后会有如下类似提示。注：提示完成后勿马上取下 U 盘。请在 SSH 会话下通过命
令 shellaccess developer 及 mc phhome status show where typein 13 检查后台收集进
度，在进度 Finished 后再等待 10 分钟后才取下 U 盘
5. 同时，在 ssh 会话下，可通过命令“mc phhome status show”显示当前收集进度。
```
# 开启命令行 sshd 服务的操作步骤
```
a) 在 DSM 里面设置控制器的 BMC IP
b) 通过浏览器访问 BMC IP ，用户名/密码：root/Calvin
c) 然后登陆虚拟控制台，登入 CML 用户名/密码
d) 执行以下命令 ，开启 sshd 服务。
i. Shellaccess developer
ii. -shell on
e) 以上执行完毕后，在客户内网可通过 ssh 客户端(如 putty) 以 ssh 方式登录 SC 存储
```