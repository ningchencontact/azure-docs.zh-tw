---
title: Microsoft Azure 資料箱磁碟的系統需求 | Microsoft Docs
description: 了解 Azure 資料箱磁碟的軟體和網路需求
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/22/2019
ms.author: alkohli
ms.openlocfilehash: 067bb6b806ddd9b83d4ea755876a980ca45d76e9
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407617"
---
# <a name="azure-data-box-disk-system-requirements"></a>Azure 資料箱磁碟系統需求

本文會針對 Microsoft Azure 資料箱磁碟解決方案以及連線至資料箱磁碟的用戶端，說明其各自的重要系統需求。 建議您先仔細檢閱此資訊再部署資料箱磁碟，之後在部署和後續作業期間若有必要，也請回頭查閱。

系統需求中包含可支援用戶端連線至磁碟的平台、支援的儲存體帳戶，以及儲存體類型。


## <a name="supported-operating-systems-for-clients"></a>支援用戶端的作業系統

以下是所支援作業系統的清單，這些系統可讓連線至資料箱磁碟的用戶端進行磁碟解除鎖定和資料複製作業。

| **作業系統** | **測試的版本** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
|  Windows |7, 8, 10 |
| Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04、16.04、18.04 <br> 8.11、9 <br> 7.0 <br> 6.5、6.9、7.0、7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows 用戶端的其他必要軟體

針對 Windows 用戶端，也應安裝下列項目。

| **軟體**| **版本** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Linux 用戶端的其他必要軟體

針對 Linux 用戶端，資料箱磁碟工具組會安裝下列必要軟體：

- dislocker
- OpenSSL

## <a name="supported-connection"></a>支援的連線

包含資料的用戶端電腦必須具有 USB 3.0 或更新版本的連接埠。 使用提供的纜線將磁碟連接到此用戶端。

## <a name="supported-storage-accounts"></a>支援的儲存體帳戶

以下是資料箱磁碟所支援的儲存體類型清單。

| **儲存體帳戶** | **注意事項** |
| --- | --- |
| 傳統 | 標準 |
| 一般用途  |標準；同時支援 V1 和 V2。 同時支援經常性儲存層和非經常性儲存層。 |
| Blob 儲存體帳戶 | |

>[!NOTE]
> 不支援 Azure Data Lake Storage Gen 2 帳戶。


## <a name="supported-storage-types-for-upload"></a>上傳的支援的存放裝置類型

以下是上傳至 Azure 中使用資料箱磁碟支援的儲存體類型的清單。

| **檔案格式** | **注意事項** |
| --- | --- |
| Azure 區塊 Blob | |
| Azure 分頁 Blob  | |
| Azure 檔案  | |
| 受控磁碟 | |


## <a name="next-step"></a>後續步驟

* [部署 Azure 資料箱磁碟](data-box-disk-deploy-ordered.md)

