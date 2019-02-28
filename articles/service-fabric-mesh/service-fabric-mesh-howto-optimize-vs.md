---
title: 將 Azure Service Fabric Mesh 專案的 Visual Studio 效能最佳化 | Microsoft Docs
description: 將 Azure Service Fabric Mesh 應用程式的 Visual Studio 效能最佳化
services: service-fabric-mesh
keywords: 將偵錯效能最佳化
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: c7c9f9e72ae7ec2807c8fea08a8cc8d3e8a9e382
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804802"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>將 Service Fabric Mesh 專案的 Visual Studio 效能最佳化

本文說明如何將 Service Fabric Mesh 專案的 Visual Studio 效能最佳化，以大幅加快您執行第一次偵錯 (F5) 的速度。  

## <a name="change-visual-studio-settings"></a>變更 Visual Studio 設定
 
在 Visual Studio 中的 [工具] > [選項]  > [Service Fabric Mesh 工具] > [一般] 下方，可以調整下列設定：

- **在專案開啟時提取所需的 Docker 映像**可藉由在專案載入時啟動映像下載程序，使您執行第一次偵錯 (F5) 的速度更快。  
- **在專案開啟時部署應用程式**可藉由在專案開啟後啟動部署程序，使您執行第一次偵錯 (F5) 的速度更快。  
- **在專案關閉時移除應用程式**會在專案關閉時移除 Mesh 應用程式，以回收配置給應用程式的資源 (CPU、RAM)。  

當您在 Service Fabric 工具輸出視窗中看到 Visual Studio 正在「提取映像」、「暖機」或「移除應用程式」的訊息時，這所指涉的是前述設定的相關資訊。 您可以關閉這些設定。

## <a name="next-steps"></a>後續步驟

完整閱讀[對 Mesh 應用程式進行偵錯的教學課程](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)