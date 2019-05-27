---
title: 設定 Azure 中 Linux VM 上的 MySQL| Microsoft Docs
description: 了解如何在 Azure 中的 Linux 虛擬機器 (Ubuntu 或 Red Hat 系列 OS) 上安裝 MySQL 堆疊
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 21ad3f9baf4b8e117f881d9a36fc606af04e17a5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158430"
---
# <a name="how-to-install-mysql-on-azure"></a>如何在 Azure 上安裝 MySQL
本文將示範如何在執行 Linux 的 Azure 虛擬機器上安裝和設定 MySQL


> [!NOTE]
> 您必須已經具有執行 Linux 的 Microsoft Azure 虛擬機器，才能完成本教學課程。 請參閱 [Azure Linux VM 教學課程](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，建立並設定以 `mysqlnode` 為 VM 名稱，`azureuser` 為使用者的 Linux VM，然後再繼續進行下一步。
> 
> 

在此情況下，請使用連接埠 3306 作為 MySQL 連接埠。  

本文的範例將使用儲存機制封裝安裝 MySQL5.6。 事實上，MySQL5.6 的效能改良功能比 MySQL5.5 更多。  詳細資訊請參閱 [這裡](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)。

## <a name="install-mysql56-on-ubuntu"></a>在 Ubuntu 上安裝 MySQL5.6
我們將使用執行 Ubuntu 的 Linux VM。


### <a name="install-mysql"></a>安裝 MySQL

藉由切換至安裝 MySQL Server 5.6`root`使用者：

```bash  
sudo su -
```

安裝 mysql-server 5.6：

```bash  
apt-get update
apt-get -y install mysql-server-5.6
```

  
在安裝期間，您會看到對話方塊視窗會出現要求您設定 MySQL 根密碼，而且您需要在這裡設定密碼。
  
![image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

再次輸入密碼以進行確認。

![image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

### <a name="sign-in"></a>登入
  
MySQL Server 安裝完成之後，MySQL 服務便會自動啟動 。 您可以登入 MySQL Server，含`root`使用者，並輸入您的密碼。

```bash  
mysql -uroot -p
```


### <a name="manage-the-mysql-service"></a>管理的 MySQL 服務

取得 MySQL 服務的狀態

```bash   
service mysql status
```
  
啟動 MySQL 服務

```bash  
service mysql start
```
  
停止 MySQL 服務

```bash  
service mysql stop
```
  
重新啟動 MySQL 服務

```bash  
service mysql restart
```

## <a name="install-mysql-on-red-hat-os-centos-oracle-linux"></a>在 OS 的 Red Hat、 CentOS、 Oracle Linux 上安裝 MySQL
以下範例將使用執行 CentOS 或 Oracle Linux 的 Linux VM。

### <a name="add-the-mysql-yum-repository"></a>將 MySQL yum 儲存機制
    
切換至`root`使用者：

```bash  
sudo su -
```

下載並安裝 MySQL 發行套件：

```bash  
wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall -y mysql-community-release-el6-5.noarch.rpm
```

### <a name="enable-the-mysql-repository"></a>啟用 MySQL 儲存機制
編輯下方檔案，以允許 MySQL 儲存機制下載 MySQL5.6 套件檔案。

```bash  
vim /etc/yum.repos.d/mysql-community.repo
```

  
將此檔案的每個值更新為下列值：

```  
\# *Enable to use MySQL 5.6*
  
[mysql56-community]
name=MySQL 5.6 Community Server
  
baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
enabled=1
  
gpgcheck=1
  
gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```

### <a name="install-mysql"></a>安裝 MySQL 

從儲存機制安裝 MySQL。

```bash  
yum install mysql-community-server
```
  
將安裝 MySQL RPM 封裝和所有相關的封裝。


## <a name="manage-the-mysql-service"></a>管理的 MySQL 服務
  
檢查服務狀態的 MySQL 伺服器：

```bash  
service mysqld status\
```
  
檢查是否正在執行的預設連接埠的 MySQL 伺服器：

```bash  
netstat  –tunlp|grep 3306
```

啟動 MySQL 伺服器：

```bash
service mysqld start
```

停止 MySQL 伺服器：

```bash
service mysqld stop
```

將 MySQL 設為啟動時啟動系統啟動：

```bash
chkconfig mysqld on
```

## <a name="install-mysql-on-suse-linux"></a>SUSE Linux 上安裝 MySQL

以下範例將使用執行 OpenSUSE 的 Linux VM。

### <a name="download-and-install-mysql-server"></a>下載及安裝 MySQL Server
  
透過下列命令切換至 `root` 使用者：  

```bash  
sudo su -
```
  
下載及安裝 MySQL Server 封裝：

```bash  
zypper update
zypper install mysql-server mysql-devel mysql
```

### <a name="manage-the-mysql-service"></a>管理的 MySQL 服務
  
檢查 MySQL 伺服器的狀態：

```bash  
rcmysql status
```
  
檢查是否在 MySQL 伺服器的預設連接埠：

```bash  
netstat  –tunlp|grep 3306
```

啟動 MySQL 伺服器：

```bash
rcmysql start
```

停止 MySQL 伺服器：

```bash
rcmysql stop
```

將 MySQL 設為啟動時啟動系統啟動：

```bash
insserv mysql
```

## <a name="next-step"></a>後續步驟
如需詳細資訊，請參閱 < [MySQL](https://www.mysql.com/)網站。

