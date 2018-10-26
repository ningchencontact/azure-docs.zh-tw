---
title: 使用 Azure Stack 更新和管理 VM | Microsoft Docs
description: 了解如何使用 Azure 自動化中的更新管理、變更追蹤及清查解決方案，來管理 Azure Stack 中部署的 Windows VM。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.openlocfilehash: 1ef20dc35b069c5f12c2f31d0979949be27271e0
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323618"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM 更新與管理
您可以使用下列 Azure 自動化解決方案功能來管理使用 Azure Stack 部署的 Windows VM：

- **[更新管理](https://docs.microsoft.com/azure/automation/automation-update-management)**。 有了更新管理解決方案，您可以快速評估所有代理程式電腦上可用更新的狀態，並管理為這些 Windows VM 安裝必要更新的程序。

- **[變更追蹤](https://docs.microsoft.com/azure/automation/automation-change-tracking)**。 受監視的伺服器上安裝的軟體、Windows 服務、Windows 登錄和檔案的變更，會傳送至雲端中的 Log Analytics 服務進行處理。 會將邏輯套用至接收的資料，且雲端服務會記錄資料。 使用 [變更追蹤] 儀表板上的資訊，您可以輕鬆地看到您的伺服器基礎結構中所做的變更。

- **[清查](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**。 Azure Stack Windows 虛擬機器的清查追蹤提供了瀏覽器式的使用者介面，可供安裝和設定清查收集。 

> [!IMPORTANT]
> 這些解決方案和用來管理 Azure VM 的解決方案相同。 Azure 和 Azure Stack Windows VM 均是以相同方式管理，使用的使用者介面和工具也相同。 搭配 Azure Stack 使用更新管理、變更追蹤及清查解決方案時，Azure Stack VM 的定價也和 Azure VM 相同。

## <a name="prerequisites"></a>必要條件
使用這些功能來更新和管理 Azure Stack Windows VM 之前，必須符合幾個必要條件， 也包括在 Azure 入口網站以及 Azure Stack 系統管理入口網站必須採取的步驟。

### <a name="in-the-azure-portal"></a>在 Azure 入口網站
若要針對 Azure Stack Windows VM 使用清查、變更追蹤及更新管理等 Azure 自動化功能，必須先在 Azure 中啟用這些解決方案。

> [!TIP]
> 如果您已在 Azure VM 上啟用這些功能，便可使用原本既有的 LogAnalytics 工作區認證。 如果您已經擁有想使用的 LogAnalytics WorkspaceID 和主索引鍵，請直接跳到[下一節](.\vm-update-management.md#in-the-azure-stack-administration-portal)。 若尚未擁有，請繼續依照本節的說明，建立新的 LogAnalytics 工作區和自動化帳戶。

啟用解決方案的第一個步驟，是在 Azure 訂用帳戶中[建立 LogAnalytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)。 Log Analytics 工作區都是唯一的 Log Analytics 環境，有自己的資料存放庫、資料來源和解決方案。 建立工作區之後，請記下 WorkspaceID 和金鑰。 若要檢視此資訊，請前往工作區刀鋒視窗、按一下 [進階設定]，然後查看 [工作區 ID] 和 [主索引鍵] 的值。 

接著必須 [建立自動化帳戶](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 自動化帳戶是 Azure 自動化資源的容器。 可供您劃分您的環境或進一步組織您的自動化工作流程和資源。 自動化帳戶建立之後，需要啟用清查、變更追蹤及更新管理功能， 請遵循下列步驟來啟用各項功能：

1. 在 Azure 入口網站中，前往您要使用的自動化帳戶。

2. 選取要啟用的解決方案 ([清查]、[變更追蹤] 或 [更新管理])。

3. 使用 [選取工作區...] 下拉式清單，選取要使用的 Log Analytics 工作區。

4. 確認其他所有資訊是否正確，然後再按一下 [啟用] 來啟用此解決方案。

5. 重複步驟 2-4，啟用共三個解決方案。 

   [![](media/vm-update-management/1-sm.PNG "啟用自動化帳戶功能")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>在 Azure Stack 系統管理入口網站
啟用 Azure 入口網站中的 Azure 自動化解決方案之後，接著需要以雲端系統管理員身分登入 Azure Stack 系統管理入口網站，並下載 **Azure 更新和組態管理**擴充功能 Azure Stack 市集項目。 

   ![Azure 更新和組態管理擴充功能市集項目](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>為 Azure Stack 虛擬機器啟用更新管理
請遵循下列步驟來啟用 Azure Stack Windows VM 的更新管理。

1. 登入 Azure Stack 使用者入口網站。

2. 在 Azure Stack 使用者入口網站中，前往要啟用這些解決方案的 Windows 虛擬機器 [擴充功能] 刀鋒視窗、按一下 [+ 新增]、選取 [Azure 更新和組態管理] 擴充功能，然後按一下 [建立]：

   [![](media/vm-update-management/3-sm.PNG "Windows VM 擴充功能刀鋒視窗")](media/vm-update-management/3-lg.PNG#lightbox)

3. 提供先前建立的 WorkspaceID 和主索引鍵，連結含有 LogAnalytics 工作區的代理程式，然後按一下 [確定] 以部署擴充功能。

   [![](media/vm-update-management/4-sm.PNG "提供 WorkspaceID 和金鑰")](media/vm-update-management/4-lg.PNG#lightbox) 

4. 如[自動化更新管理說明文件](https://docs.microsoft.com/azure/automation/automation-update-management)中所述，您必須在想要加以管理的各個 VM 上啟用更新管理解決方案。 若要在會回報至工作區的所有 VM 上啟用解決方案，請選取 [更新管理]，按一下 [管理機器]，然後選取 [在所有可用及未來的機器上啟用] 選項。

   [![](media/vm-update-management/5-sm.PNG "提供 WorkspaceID 和金鑰")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > 重複此步驟，在會回報至工作區的 Azure Stack Windows VM 上啟用每個解決方案。 
  
啟用 Azure 更新和組態管理擴充功能之後，每天會對各個受控的 Windows VM 掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否已變更。 如果狀態已變更，則會起始合規性掃描。

掃描 VM 之後，VM 即會出現在更新管理解決方案的 Azure 自動化帳戶中： 

   [![](media/vm-update-management/6-sm.PNG "提供 WorkspaceID 和金鑰")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 儀表板可能需要 30 分鐘到 6 小時，才能顯示來自受控電腦的已更新資料。

Azure Stack Windows VM 現在已和 Azure VM 一同納入排程的更新部署中。

## <a name="enable-update-management-using-a-resource-manager-template"></a>使用 Resource Manager 範本啟用更新管理
如果您有大量的 Azure Stack Windows VM，可以使用[這個 Azure Resource Manager 範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)，更輕鬆地在 Windows VM 上部署解決方案。 此範本會將 Microsoft Monitoring Agent 擴充功能部署到現有的 Windows VM，並將擴充功能新增至現有的 Azure LogAnalytics 工作區。
 
## <a name="next-steps"></a>後續步驟
[將 SQL Server 效能最佳化](azure-stack-sql-server-vm-considerations.md)
