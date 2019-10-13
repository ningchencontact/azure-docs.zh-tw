---
title: 我的 ASP.NET 5 專案（Visual Studio 連接服務）發生了什麼事
description: 說明使用 Visual Studio 已連接服務連接到 Visual Studio ASP.NET 5 專案中的 Azure 儲存體帳戶後，會發生什麼事
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300010"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>我的 ASP.NET 5 專案 (Visual Studio Azure 儲存體連接的服務) 發生什麼狀況？
## <a name="references-added"></a>加入參考
Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。  
這個封裝會加入下列 .NET 參考：

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

另外也加入了 NuGet 封裝 **Microsoft.Framework.Configuration.Json** 。

## <a name="connection-string-for-azure-storage-added"></a>加入 Azure 儲存體的連接字串
在專案的 config.json 檔案中，已使用所選儲存體帳戶的連接字串和金鑰建立一個元素。

如需詳細資訊，請參閱 [ASP.NET 5](https://www.asp.net/vnext)。

