---
title: 刪除及還原 Azure Log Analytics 工作區 |Microsoft Docs
description: 了解如何刪除您的 Log Analytics 工作區 (如果您已在個人訂用帳戶中建立工作區) 或重組您的工作區模型。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: magoedte
ms.openlocfilehash: 4f03fc71a11c1ecb2e96b316efac9249395fc333
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285535"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>刪除和還原 Azure Log Analytics 工作區
本文說明 Azure Log Analytics 工作區虛刪除的概念，以及如何復原已刪除的工作區。 

## <a name="considerations-when-deleting-a-workspace"></a>刪除工作區時的考慮
當您刪除 Log Analytics 工作區時，會執行虛刪除作業，以允許在14天內復原工作區（包括其資料和連線的代理程式），不論是意外或刻意刪除。 在虛刪除期間之後，工作區和其資料無法復原，而且會在30天內加入永久刪除佇列。

當您刪除工作區時，想要特別小心，因為可能有重要的資料和設定可能會對您的服務作業造成負面影響。 審查哪些代理程式、解決方案和其他 Azure 服務，以及將其資料儲存在 Log Analytics 中的來源，例如：
* 管理解決方案
* Azure 自動化
* 在 Windows 與 Linux 虛擬機器上執行的代理程式
* 在您環境中的 Windows 與 Linux 電腦上執行的代理程式
* System Center Operations Manager

虛刪除作業會刪除工作區資源，且任何相關聯的使用者許可權都會中斷。 如果使用者與其他工作區相關聯，則他們可以繼續搭配其他工作區使用 Log Analytics。

## <a name="soft-delete-behavior"></a>虛刪除行為
工作區刪除作業會移除工作區 Resource Manager 資源，但其設定和資料會保留14天，同時提供刪除工作區的外觀。 設定為向工作區報告的任何代理程式和 System Center Operations Manager 管理群組，在虛刪除期間都會維持在孤立狀態。 服務會進一步提供一種機制來復原已刪除的工作區，包括其資料和連線的資源，基本上就是將刪除作業還原。

> [!NOTE] 
> 已安裝的解決方案和已連結的服務（例如自動化帳戶）會在刪除時從工作區中永久移除，而且無法復原。 這些應該在復原作業之後重新設定，將工作區帶到其先前的功能。 

您可以使用[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、 [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)或[Azure 入口網站](https://portal.azure.com)來刪除工作區。

### <a name="delete-workspace-in-azure-portal"></a>刪除 Azure 入口網站中的工作區
1. 若要登入，請移至[Azure 入口網站](https://portal.azure.com)。 
2. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。
3. 在 Log Analytics 工作區清單中，選取工作區，然後按一下中間窗格頂端的 [**刪除**]。
   ![來自工作區屬性窗格的刪除選項](media/delete-workspace/log-analytics-delete-workspace.png)
4. 當要求您確認刪除工作區的確認訊息窗格出現時，按一下 [是]。
   ![確認刪除工作區](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>復原工作區
如果您具有訂用帳戶和資源群組的參與者許可權，且該工作區在虛刪除作業之前建立關聯，您可以在其虛刪除期間（包括其資料、設定和連接的代理程式）進行復原。 在虛刪除期間之後，工作區無法復原，而且會被指派永久刪除。

只要這些屬性已填入已刪除的工作區詳細資料，您就可以使用[PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace)或[REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)工作區建立方法來重新建立工作區，包括：
1.  訂用帳戶識別碼
2.  資源群組名稱
3.  工作區名稱
4.  區域

> [!NOTE]
> * [Azure 入口網站](https://portal.azure.com)不支援工作區復原。 在虛刪除期間重新建立工作區，表示此工作區名稱已在使用中。
> * 已刪除之工作區的名稱會保留以進行虛刪除期間，而且無法在建立新的工作區時使用。
