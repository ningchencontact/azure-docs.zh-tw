---
title: 如何啟動、停止和刪除您的 Azure Spring Cloud 應用程式 | Microsoft Docs
description: 如何啟動、停止和刪除您的 Azure Spring Cloud 應用程式
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038437"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>如何啟動、停止和刪除您的 Azure Spring Cloud 應用程式

本指南會說明如何使用 Azure 入口網站或 CLI 來變更 Azure Spring Cloud 中應用程式的狀態。

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

在您部署應用程式之後，便可以使用 Azure 入口網站來**啟動**、**停止**及**刪除**它。

1. 在 Azure 入口網站中，移至您的 Azure Spring Cloud 服務執行個體。
1. 選取 [應用程式儀表板]  索引標籤。
1. 選取您想要變更狀態的應用程式。
2. 在該應用程式的 [概觀]  頁面中，尋找按鈕來 [啟動/停止]  、[重新啟動]  ，以及 [刪除]  該應用程式。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 您可以搭配 Azure CLI 來使用選擇性參數及設定預設值。 若要深入了解，請閱讀我們的[參考文件](spring-cloud-cli-reference.md)。

* 啟動您的應用程式：
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 停止您的應用程式：
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 重新啟動您的應用程式：
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 刪除您的應用程式：
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
