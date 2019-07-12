---
title: 新增 Azure Migrate 中的評估工具 |Microsoft Docs
description: 描述如何在 Azure Migrate 中樞新增評估工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811644"
---
# <a name="add-assessment-tools"></a>新增評量工具

本文說明如何新增在評量工具[Azure Migrate](migrate-overview.md)。

Azure Migrate 會評定及移轉工具的中樞提供至 Azure。 它包含原生工具、 其他 Azure 服務和第三方獨立軟體廠商 (ISV) 供應項目所提供的工具。

如果您想要新增評估工具，您還沒有 Azure Migrate 專案，請遵循這[文章](how-to-add-tool-first-time.md)。

## <a name="selecting-an-isv-tool"></a>選取的 ISV 工具

如果您選擇[ISV 工具](migrate-services-overview.md#isv-integration)進行評估，您可以開始取得授權，或註冊免費試用版，根據 ISV 的原則。 在每個工具，沒有連接到 Azure Migrate 的選項。 遵循指示工具和文件連線使用 Azure Migrate 的 [工具] 工作區。 


## <a name="select-an-assessment-scenario"></a>選取評估案例

1. 在 Azure Migrate 專案中，按一下**概觀**。
2. 選取您想要使用的評估案例：

    - 若要探索及評估機器和工作負載移轉至 Azure，請選取**評定及移轉伺服器**。
    - 若要評估內部部署 SQL 機器，請選取**評定，移轉資料庫**。
    - 若要評估內部部署 web 應用程式，請選取**評定及移轉 web 應用程式**。

    ![評估案例](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>選取伺服器評估工具 

1. 按一下 **評估，以及移轉伺服器**。
2. 在  **Azure Migrate-伺服器**，如果您尚未在加入評估工具，**評量工具**，選取**按一下這裡以加入評估工具**。 如果您已新增在評量工具**新增更多的評估工具**，選取**變更**。

    > [!NOTE]
    > 如果您要在瀏覽至不同的專案中， **Azure Migrate-伺服器**旁的 **查看不同的移轉專案的詳細資料**，按一下 **按一下這裡**。

3. 在  **Azure Migrate**，選取您想要使用的評估工具。

    
    ![評量工具](./media/how-to-assess/assess-tool.png)

    - 如果您使用 Azure Migrate Server 評量，您可以設定、 執行和檢視直接在 Azure Migrate 專案中的評量。
    - 如果您使用第三方評定工具，請瀏覽至其站台，提供的連結，並執行評估根據它們所提供的指示。


## <a name="select-a-database-assessment-tool"></a>選取資料庫評估工具

1. 按一下 **評定，移轉資料庫**
2. 在 **資料庫**，按一下**將工具新增**。
3. 在 加入工具 >**選取評估工具**，選取您想要用來評估您的資料庫的工具。

## <a name="select-a-web-app-assessment-tool"></a>選取 web 應用程式評估工具

1. 按一下 **評定及移轉 web 應用程式**。
2. Azure App Service，遵循移轉工具的連結。 使用移轉工具：

    - **評估應用程式線上**:您可以使用 Azure App Service 移轉小幫手評估線上的公用 url 的應用程式。
    - **.NET/PHP**:針對內部的.NET 和 PHP 應用程式，您可以下載並執行移轉小幫手。



## <a name="next-steps"></a>後續步驟

嘗試使用 Azure Migrate Server 評量的評定[HYPER-V](tutorial-prepare-hyper-v.md)或是[VMware](tutorial-prepare-vmware.md) Vm。
