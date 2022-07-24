# win11 修改时间颜色

打开注册表 `regedit`  进入

~~~bash
计算机\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
~~~

将`ColorPrevalence` 的数据修改为 `1` 就变成白色了。 `0` 是黑色

将`SystemUsesLightTheme` 的数据修改为`0` 可以把黑色的小图标改成白色

# win11 打开防火墙需要新下软件

用管理员权限打开 PowerShell ，依次输入下面3个命令

~~~bash
Set-ExecutionPolicy Unrestricted
~~~

~~~bash
Get-AppXPackage -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register “（ （（_.InstallLocation）\AppXManifest.xml”}
~~~

~~~bash
Get-AppxPackage Microsoft.SecHealthUI -AllUsers | Reset-AppxPackage
~~~

# 微软账号登不上

可以更改dns解决，将dns更改为

~~~bash
-- 4.2.2.2 
~~~

# 透明化任务栏

> 都在Microsoft Store中下载

1. 使用TranslucentTB
2. 如果任务栏出现灰色横线，在使用RoundedTB，将  **margin设置为1**
