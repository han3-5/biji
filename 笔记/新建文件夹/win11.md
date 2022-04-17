# win11 修改时间颜色

打开注册表 `regedit`  进入

~~~bash
计算机\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize
~~~

将`ColorPrevalence` 的数据修改为 `1` 就变成白色了。

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

