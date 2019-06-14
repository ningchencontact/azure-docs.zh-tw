---
title: 我的雲端服務專案發生什麼狀況？ | Microsoft Docs
description: 說明使用 Visual Studio 已連接服務連接至 Azure 儲存體帳戶後，雲端服務專案發生的狀況
services: storage
author: ghogen
manager: douge
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: b0adceb42109f07658e56cebde3fbc4401478a1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720453"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>我的雲端服務專案發生什麼狀況 (Visual Studio Azure 儲存體已連接服務)？
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

## <a name="connection-string-for-azure-storage-added"></a>加入 Azure 儲存體的連接字串
已使用所選儲存體帳戶的連接字串和金鑰建立元素。 已修改下列檔案：

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

