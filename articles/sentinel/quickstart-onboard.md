---
title: 在 Azure 的 Sentinel 預覽上架 |Microsoft Docs
description: 了解如何在 Azure Sentinel 中收集資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/12/2019
ms.author: rkarlin
ms.openlocfilehash: c055b3d9dec2e2538a7dc7ac0eb707c97313d0d6
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006598"
---
# <a name="on-board-azure-sentinel-preview"></a>內建的 Azure Sentinel 預覽

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在本快速入門中，您將學習如何將 Azure Sentinel。 

若要將 Azure Sentinel 上架，必須先連線到資料來源。 Azure 的 Sentinel 隨附了多種連接器的方塊，並提供即時的整合，包括 Microsoft 威脅防護解決方案，Microsoft 365 來源，包括 Office 365，Azure AD 中，Azure ATP，從可用的 Microsoft 解決方案和Microsoft Cloud App Security，等等。 此外，還有適用於非 Microsoft 解決方案的內建連接器，用於連線至更廣泛的安全性生態系統。 您也可以使用常見事件格式，將資料來源連接與 Azure Sentinel Syslog 或 REST API。  

將資料來源的連接之後，請選擇從範本庫建立評閱呈現資料為基礎的深入解析儀表板。 您的需求，可以輕鬆地自訂這些儀表板。


## <a name="global-prerequisites"></a>通用的必要條件

- 作用中 Azure 訂用帳戶，如果您沒有帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)開始之前。

- Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](../log-analytics/log-analytics-quick-create-workspace.md)

    - 繫結至 Azure 的 Sentinel 的資源群組的 「 參與者 」 權限
    - 您的資源群組的 「 參與者 」 權限，才能啟用 Azure Sentinel 

## 啟用 Azure 的 Sentinel <a name="enable"></a>

1. 移至 Azure 入口網站。
2. 請確定已選取 Azure Sentinel 建立所在的訂用帳戶。 
3. Azure 的 Sentinel 搜尋。 
   ![搜尋](./media/quickstart-onboard/search-product.png)

1. 按一下 [+新增]。
1. 選取您想要使用或建立一個新的工作區。 您可以執行 Azure Sentinel 上多個工作區中，但資料完全隔離至單一工作區。

   ![搜尋](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **工作區位置**務必要了解您串流處理至 Azure 的 Sentinel 的所有資料都會都儲存在您所選工作區的地理位置。  
   > - Azure 資訊安全中心所建立的預設工作區不會出現在清單中;您無法在其上安裝 Azure Sentinel。
   > - Azure 的 Sentinel 可以部署在下列任何一個區域中的工作區上執行：澳洲東南部、 加拿大中部、 印度中部、 美國東部、 美國東部 2 EUAP (Canary)，日本東部、 東南亞、 英國南部、 西歐、 美國西部 2。

6. 按一下 **加入 Azure 的 Sentinel**。
  

## <a name="connect-data-sources"></a>連線資料來源

Azure 的 Sentinel 會藉由連線至服務，並將事件和記錄檔轉送到 Azure 的 Sentinel 建立服務和應用程式的連接。 機器和虛擬機器，您可以安裝 Azure Sentinel 的代理程式可收集記錄檔，並將它們轉送到 Azure 的 Sentinel。 針對防火牆和 proxy，Azure Sentinel，將會使用 Linux Syslog 伺服器。 在其上安裝代理程式，從代理程式以收集記錄檔並將它們轉送到 Azure 的 Sentinel。 
 
1. 按一下 **資料收集**。
2. 沒有您可以連接的每個資料來源的圖格。<br>
例如，按一下**Azure Active Directory**。 如果您要連接此資料來源，您會將從 Azure AD 的所有記錄檔串流至 Azure 的 Sentinel。 您可以選取您要取得-記錄何種單一登入及/或稽核記錄檔。 <br>
在底部，Azure Sentinel 提供的建議的儀表板，您應該安裝每個連接器讓您可以立即取得有趣的深入解析您的資料。 <br> 遵循安裝指示或[，請參閱相關的連接指南](connect-data-sources.md)如需詳細資訊。 如需資料連接器的資訊，請參閱[連接的 Microsoft 服務](connect-data-sources.md)。

在您的資料來源連接之後, 您的資料開始串流處理至 Azure 的 Sentinel，並且可供您開始使用。 您可以檢視中的記錄檔[內建儀表板](quickstart-get-visibility.md)，並開始建立 Log Analytics 中的查詢[調查資料](tutorial-investigate-cases.md)。



## <a name="next-steps"></a>後續步驟
在本文件中，您會了解資料來源連接至 Azure 的 Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
- Stream 中的資料[常見的錯誤格式設備](connect-common-event-format.md)到 Azure 的 Sentinel。
