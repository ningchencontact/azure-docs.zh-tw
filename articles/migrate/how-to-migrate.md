---
title: 新增 Azure Migrate 中的移轉工具
description: 描述如何在 Azure Migrate 中樞新增移轉工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811865"
---
# <a name="add-migration-tools"></a>新增移轉工具

本文說明如何新增中的移轉工具[Azure Migrate](migrate-overview.md)。

Azure Migrate 會評定及移轉工具的中樞提供至 Azure。 它包含原生工具、 其他 Azure 服務和第三方獨立軟體廠商 (ISV) 供應項目所提供的工具。

如果您想要新增的移轉工具，而且您尚未設定 Azure Migrate 專案，請遵循這[文章](how-to-add-tool-first-time.md)。



## <a name="selecting-an-isv-tool"></a>選取的 ISV 工具

如果您選擇[ISV 工具](migrate-services-overview.md#isv-integration)移轉，您可以開始取得授權，或註冊免費試用版，根據 ISV 的原則。 在每個工具，沒有連接到 Azure Migrate 的選項。 部署工具，並遵循工具指示和文件使用 Azure Migrate 連接工具工作區。 

## <a name="select-a-migration-scenario"></a>選取移轉案例

1. 在 Azure Migrate 專案中，按一下**概觀**。
2. 選取您想要使用的移轉案例：

    - 若要將機器和工作負載移轉至 Azure 中，選取**評定及移轉伺服器**。
    - 若要移轉內部部署 SQL 機器，請選取**評定，移轉資料庫**。
    - 若要移轉內部部署 web 應用程式，請選取**評定及移轉 web 應用程式**。
    - 若要將大量的內部部署資料移轉至 Azure，在離線模式中，選取**訂購資料箱**。

    ![評估案例](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>選取的伺服器移轉工具

1. 按一下 **評估，以及移轉伺服器**。
2. 在  **Azure Migrate-伺服器**，如果您尚未新增移轉工具下,**移轉工具**，選取**按一下這裡可新增的移轉工具**。 如果您已在新增移轉工具**新增更多移轉工具**，選取**變更**。

    > [!NOTE]
    > 如果您要在瀏覽至不同的專案中， **Azure Migrate-伺服器**旁的 **查看不同的移轉專案的詳細資料**，按一下 **按一下這裡**。

3. 在  **Azure Migrate**，選取您想要使用的移轉工具。
    - 如果您使用 Azure 移轉的伺服器移轉時，您可以設定，並直接在 Azure Migrate 專案中執行移轉。
    - 如果您使用第三方評定工具，請巡覽至的 ISV，提供的連結，並執行移轉根據它們所提供的指示。

## <a name="select-a-database-migration-tool"></a>選取資料庫移轉工具

1. 按一下 **評定，移轉資料庫**
2. 在 **資料庫**，按一下**將工具新增**。
3. 在 加入工具 >**選取的移轉工具**，選取您想要將資料庫移轉的工具。

## <a name="select-a-web-app-migration-tool"></a>選取 web 應用程式移轉工具

1. 按一下 **評定及移轉 web 應用程式**。
2. Azure App Service，遵循移轉工具的連結。 使用移轉工具：

    - **評估應用程式線上**:您可以評估，並使用 Azure App Service 移轉小幫手移轉線上的公用 url 的應用程式。
    - **.NET/PHP**:針對內部的.NET 和 PHP 應用程式，您可以下載並執行移轉小幫手。

## <a name="order-an-azure-data-box"></a>Azure 資料箱訂單

若要將大量資料移轉至 Azure，您可以訂購的離線資料傳輸的 Azure 資料箱。

1. 按一下 **訂購資料箱**。
2. 在 **選取您的 Azure 資料箱**，指定您的訂用帳戶。 
3. 傳輸速度將會匯入至 Azure。 指定資料來源，以及資料的 Azure 區域目的地。

## <a name="next-steps"></a>後續步驟

試試看使用的 Azure 移轉伺服器移轉的移轉[HYPER-V](tutorial-migrate-hyper-v.md)或是[VMware](tutorial-migrate-vmware.md) Vm。
