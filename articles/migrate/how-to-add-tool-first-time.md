---
title: 在 Azure Migrate 中的第一次將評量/移轉工具 |Microsoft Docs
description: 描述如何建立 Azure Migrate 專案並加入評估/移轉工具。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812021"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>第一次將評量/移轉工具

這篇文章說明如何將評定或移轉的工具，加入[Azure Migrate](migrate-overview.md)第一次的專案。  
Azure Migrate 提供中央的中樞，以追蹤探索、 評估及移轉內部部署應用程式和工作負載和私密/公開金鑰雲端 Vm 至 Azure。 中樞的評估和移轉，以及第三方、 獨立軟體廠商 (ISV) 提供 Azure 移轉工具[供應項目](migrate-services-overview.md#isv-integration)。 

## <a name="create-a-project-and-add-a-tool"></a>建立專案，並加入工具

設定的新 Azure Migrate 專案中的 Azure 訂用帳戶，並新增一項工具。

- Azure Migrate 專案用來儲存探索、 評估和移轉從您正在評估或移轉的環境收集的中繼資料。 
- 在專案中，您可以追蹤探索到的資產，並協調評定及移轉。

1. 在 Azure 入口網站 >**所有的服務**，搜尋**Azure Migrate**。
2. 底下**Services**，選取**Azure Migrate**。

    ![設定 Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. 在 **概觀**，按一下**評定及移轉伺服器**。
4. 底下**探索、 評估及移轉伺服器**，按一下**評定以及移轉伺服器**。

    ![探索及評定伺服器](./media/how-to-add-tool-first-time/assess-migrate.png)

1. 在**探索、 評估及移轉伺服器**，按一下**將工具新增**。
2. 在 **移轉專案**選取您 Azure 訂用帳戶，如果您還沒有建立資源群組。
3. 在 **專案的詳細資料**，指定專案名稱，以及您想要建立專案所在的地理位置。 

    ![建立 Azure Migrate 專案](./media/how-to-add-tool-first-time/migrate-project.png)

    您可以在任何這些地理位置中建立 Azure Migrate 專案。

    **地理位置** | **儲存體位置的區域**
    --- | ---
    亞洲 | 東南亞或東亞
    歐洲 | 南歐或西歐
    英國 | 英國南部或英國西部
    美國 | 美國中部 」 或 「 美國西部 2

    針對專案所指定的地理位置，僅會用於儲存從內部部署虛擬機器收集的中繼資料。 您可以選取任何實際移轉的目標區域。

4. 按一下 [**下一步]** ，並將新增的評定或移轉的工具。

    > [!NOTE]
    > 當建立專案時，您必須新增至少一個評定或移轉的工具。

5. 在 **選取評估工具**，將評估工具。 如果您不需要評估工具，請選取**略過加入評估工具現在** > **下一步**。 
2. 在 **選取的移轉工具**，視需要新增的移轉工具。 如果您現在不需要移轉工具，請選取**略過加入目前的移轉工具** > **下一步**。
3. 在 **檢閱 + 將工具加入**，檢閱設定，然後按一下 **將工具新增**。

建立專案之後，您可以選取評定及移轉的伺服器和工作負載、 資料庫和 web 應用程式的其他工具。

## <a name="create-additional-projects"></a>建立其他的專案

在某些情況下，您可能需要建立額外的 Azure Migrate 專案。 例如，如果您將資料中心在不同的地理位置，或您需要針對不同的地理位置中儲存中繼資料。 請建立其他專案，如下所示：

1. 在目前 Azure Migrate 專案中，按一下**伺服器**或是**資料庫**。
2. 在右上角，按一下**變更**目前的專案名稱旁邊。
3. 在 **設定**，選取**建立新的專案，請按一下這裡**。
4. 建立新的專案，並加入新的工具，如先前程序中所述。

## <a name="next-steps"></a>後續步驟

了解如何新增額外[評定](how-to-assess.md)並[移轉](how-to-migrate.md)工具。 
