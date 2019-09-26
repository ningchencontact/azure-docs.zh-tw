---
title: 在 Azure Sentinel 上架 |Microsoft Docs
description: 瞭解如何在 Azure Sentinel 中收集資料。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 7f042cfe10bd8ca57d9a2dae511a13a82f053a67
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316820"
---
# <a name="on-board-azure-sentinel"></a>Azure Sentinel 的面板



在本快速入門中，您將瞭解如何 Azure Sentinel 的面板。 

若要在 Azure Sentinel 上，您必須先啟用 Azure Sentinel，然後再連接您的資料來源。 Azure Sentinel 隨附一些適用于 Microsoft 解決方案的連接器，現成可用並提供即時整合，包括 Microsoft 威脅防護解決方案、Microsoft 365 來源，包括 Office 365、Azure AD、Azure ATP 及Microsoft Cloud App Security，還有更多。 此外，還有適用於非 Microsoft 解決方案的內建連接器，用於連線至更廣泛的安全性生態系統。 您也可以使用常見的事件格式、Syslog 或 REST API，將您的資料來源與 Azure Sentinel 連線。  

連接您的資料來源之後，請從以您的資料為基礎的專家建立之活頁簿庫中選擇。 這些活頁簿可以根據您的需求輕鬆地進行自訂。


## <a name="global-prerequisites"></a>全域必要條件

- 使用中的 Azure 訂用帳戶（如果您沒有的話），請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Log Analytics 工作區。 了解如何[建立 Log Analytics 工作區](../log-analytics/log-analytics-quick-create-workspace.md)。 如需 Log Analytics 工作區的詳細資訊，請參閱[設計您的 Azure 監視器記錄部署](../azure-monitor/platform/design-logs-deployment.md)。

-  若要啟用 Azure Sentinel，您需要 Azure Sentinel 工作區所在之訂用帳戶的參與者許可權。 
- 若要使用 Azure Sentinel，您需要工作區所屬資源群組的「參與者」或「讀者」許可權。
- 連接特定資料來源可能需要其他許可權。
- Azure Sentinel 是付費服務。 如需定價資訊，請參閱[關於 Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058)。
 
## 啟用 Azure Sentinel<a name="enable"></a>

1. 進入 Azure 入口網站。
2. 請確定已選取 Azure Sentinel 建立所在的訂用帳戶。 
3. 搜尋 Azure Sentinel。 
   ![search](./media/quickstart-onboard/search-product.png)

1. 按一下 [+新增]。
1. 選取您想要使用的工作區，或建立一個新的。 您可以在一個以上的工作區上執行 Azure Sentinel，但資料會隔離到單一工作區。

   ![搜尋](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Azure 資訊安全中心所建立的預設工作區不會出現在清單中;您無法在其上安裝 Azure Sentinel。
   > - Azure Sentinel 可以在 Log Analytics 的任何[GA 區域](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)中的工作區上執行，但中國、德國和 Azure Government 區域除外。 Azure Sentinel 所產生的資料（例如事件、書簽和警示規則，其中可能包含來自這些工作區的一些客戶資料）會儲存在西歐（適用于位於歐洲的工作區）或美國東部（適用于所有美國的工作區，以及歐洲以外的任何其他區域。

6. 按一下 [**新增 Azure Sentinel**]。
  

## <a name="connect-data-sources"></a>連線資料來源

Azure Sentinel 藉由連接到服務並將事件和記錄轉送至 Azure Sentinel，來建立與服務和應用程式的連接。 針對電腦和虛擬機器，您可以安裝收集記錄的 Azure Sentinel 代理程式，然後將它們轉送至 Azure Sentinel。 對於防火牆和 proxy，Azure Sentinel 利用 Linux Syslog 伺服器。 代理程式會安裝在其上，而代理程式會從中收集記錄檔，並將其轉送至 Azure Sentinel。 
 
1. 按一下 [**資料收集**]。
2. 您可以連接的每個資料來源都有一個磚。<br>
例如，按一下 [ **Azure Active Directory**]。 如果您連接此資料來源，您可以將 Azure AD 的所有記錄串流至 Azure Sentinel。 您可以選取您要用來取得登入記錄及/或審核記錄的記錄類型。 <br>
在底部，Azure Sentinel 會提供建議，讓您針對每個連接器安裝的活頁簿，讓您可以立即取得資料的相關深入解析。 <br> 依照安裝指示進行，或[參閱相關的連線指南](connect-data-sources.md)以取得詳細資訊。 如需資料連線器的詳細資訊，請參閱[連接 Microsoft 服務](connect-data-sources.md)。

連接資料來源之後，您的資料會開始串流至 Azure Sentinel，並準備好開始使用。 您可以在內[建儀表板](quickstart-get-visibility.md)中查看記錄，並開始在 Log Analytics 中建立查詢來[調查資料](tutorial-investigate-cases.md)。



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將資料來源連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- 將[常見錯誤格式設備](connect-common-event-format.md)的資料串流至 Azure Sentinel。
