---
layout: post
title:  WSL安装Docker
date:   2019-05-24 17:19:00 +0800
categories: Docker
tag: Docker
---

* content
{:toc}



## 一、安装环境
### 1、Window版本
![变量编辑入口-02](/images/2019-05-24/wsl-docker-1.png)

### 2、WSL
安装Ubuntu18.04 LTS，安装教程可以上网查询window10开启WSL，这里不作讲述。

![变量编辑入口-02](/images/2019-05-24/wsl-docker-2.png)

## 二、安装过程
### 1、安装Docker
启动WSL控制台，执行以下指令
```bash
sudo apt update
sudo apt install docker.io
sudo usermod -aG docker $USER
```

### 2、以管理员运行WSL控制台，执行下面脚本
```bash
sudo cgroupfs-mount
sudo service docker start
```

### 2、验证安装
重新开启一个WSL控制台，执行下面脚本
```bash
docker version
```
执行结果：
<br/>

![变量编辑入口-02](/images/2019-05-24/wsl-docker-3.png)
没有报错，验证通过。

## 三、遗留问题
每次重启电脑，需要使用管理员权限重新启动docker服务，才能使用docker服务。

这里编写了一个脚本自动运行，可保存为bat或cmd文件，重启系统后双击运行即可。

```bat
@echo off
chcp 65001
rem 启动WSL的Dokcer服务
cls

>nul 2>&1 "%SYSTEMROOT%\system32\cacls.exe" "%SYSTEMROOT%\system32\config\system"
if '%errorlevel%' NEQ '0' (
  echo 请求管理员权限...
  goto UACPrompt
) else ( goto gotAdmin )


:UACPrompt
	echo Set UAC = CreateObject^("Shell.Application"^) > "%temp%\getadmin.vbs"
	echo UAC.ShellExecute "%~s0", "", "", "runas", 1 >> "%temp%\getadmin.vbs"
	"%temp%\getadmin.vbs"
exit /B


:gotAdmin
  wsl -u root service docker start
  pause
  rem bash & sudo cgroupfs-mount & sudo service docker start & service docker status
goto:eof
```