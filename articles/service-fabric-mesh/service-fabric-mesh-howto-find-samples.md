---
title: 尋找 Azure Service Fabric Mesh 範例 | Microsoft Docs
description: 了解如何尋找不同的 Service Fabric Mesh 範例應用程式。
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: db8c68bf5f9aeb8069044c1344be9f69e498b1b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419555"
---
# <a name="find-service-fabric-mesh-samples"></a>尋找 Service Fabric Mesh 範例

下表列出可用的 Service Fabric Mesh 範例應用程式。 這些範例中的原始程式碼可說明如何使用 Service Fabric 資源模型達成特定案例。

若想進一步了解直接部署至 Azure 的部署範本，請參閱[範例範本的 GitHub 頁面](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)。


|範例範本|案例描述|原始程式碼|開發人員工具|
|------------|--------------------|----------|----------------------|
| [Hello World 應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | 裝載於容器中的靜態網頁。 在 Linux 上它會使用 nginx，在 Windows 上則使用 IIS | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 沒有需求 |
| [Azure 檔案磁碟區的計數器應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | 在容器中掛接以 Azure 檔案為基礎的磁碟區來儲存狀態。 <br><br> **附註：** 若要使用此範本，必須已佈建 Azure 檔案服務的檔案共用：[指示](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 工具 |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | 建立具有前端和後端服務 (使用 DNS 解析) 的應用程式。 請參閱[這裡](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore)的教學課程 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh 工具 |
| [視覺物件應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | 調整和升級應用程式內的微服務。 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh 工具 |
| [投票應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | 建立具有前端和後端服務 (使用 DNS 解析) 的應用程式 | [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Windows 版的 Visual Studio Mesh 工具 (VS Code/dotnet CLI) 可用於 Linux 版本 |
| [Service Fabric 可靠磁碟區的計數器應用程式](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| 在容器中掛接以 Service Fabric 可靠磁碟為基礎的磁碟區來儲存狀態。| [原始程式碼](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh 工具 |
