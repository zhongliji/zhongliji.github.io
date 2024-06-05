# 格式化U盘为fat32格式
# U盘根目录下创建unlock.phy    里面输入unlock Admin
# 将U盘插到当前的Leader controller上（如果不知道可以分别试一下）
# 此时可以通过管理软件或者串口使用账号：Admin，密码：mmm登录存储
# 如果不知道IP，可以通过串口登录，通过system show和cont show查看IP
# 在EM当中修改密码（修改完密码之前不要拔出U盘）
# 修改完密码以后，拔出U盘，使用新密码重新登陆即可