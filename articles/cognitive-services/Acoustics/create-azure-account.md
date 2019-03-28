---
title: 專案樂器 Azure Batch 帳戶設定
titlesuffix: Azure Cognitive Services
description: 此操作說明描述專案樂器 Unity 和 Unreal 引擎整合搭配 Azure Batch 帳戶的設定。
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309668"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>專案樂器 Azure Batch 帳戶設定
此操作說明描述專案樂器 Unity 和 Unreal 引擎整合搭配 Azure Batch 帳戶的設定。

## <a name="get-an-azure-subscription"></a>取得 Azure 訂用帳戶
您必須先有 [Azure 訂用帳戶](https://azure.microsoft.com/free/)，才能設定 Batch 與「儲存體」帳戶。 如果您是第一次註冊，Azure 會提供一些有時間限制的免費資源與 200 美元的信用額度。

## <a name="create-azure-batch-and-storage-accounts"></a>建立 Azure Batch 與儲存體帳戶
接著，請依照[這些指示](https://docs.microsoft.com/azure/batch/batch-account-create-portal)來設定 Azure Batch 和相關的「Azure 儲存體」帳戶。

為 Batch 與「儲存體」帳戶選取預設選項：
  
  ![螢幕擷取畫面 Azure Batch 的新帳戶顯示的預設設定的選項](media/new-batch-account-create.png)

  ![螢幕擷取畫面的 Azure 儲存體新帳戶顯示的預設設定的選項](media/batch-storage-account-create.png)

Azure 需要幾分鐘的時間來部署帳戶。 在入口網站的右上角即可找到完整的通知。
  
  ![螢幕擷取畫面的 Azure 帳戶部署通知](media/batch-accounts-deploy-notification.png)

您現在應該可以在儀表板上看到您的帳戶。
  
  ![螢幕擷取畫面的 Azure 入口網站儀表板顯示的 Batch 和儲存體帳戶](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 認證來設定聲場製作 UI
按一下儀表板上的 Batch 帳戶連結，然後按一下 Batch 帳戶頁面上的 [金鑰] 連結以存取您的認證。
  
  ![反白顯示的 [金鑰] 頁面連結的螢幕擷取畫面的 Azure Batch 帳戶](media/batch-access-keys.png)

  ![螢幕擷取畫面的 Azure Batch 帳戶金鑰 頁面以存取金鑰](media/batch-keys-info.png)

按一下頁面上的 [儲存體帳戶] 連結，以存取您的「Azure 儲存體」帳戶認證。
  
  ![螢幕擷取畫面的 Azure 儲存體帳戶金鑰 頁面以存取金鑰](media/storage-keys-info.png)

輸入中的這些認證[Unity 製作外掛程式](unity-baking.md)或是[Unreal 製作外掛程式](unreal-baking.md)。

## <a name="node-types-and-region-support"></a>節點類型與區域支援
專案樂器需要 Fsv2 和 H 系列計算最佳化可能不支援在所有 Azure 區域的 Azure VM 節點。 請參閱[此表格](https://azure.microsoft.com/global-infrastructure/services)，以確保您為 Batch 帳戶選取的位置正確。
![顯示依區域的 Azure 虛擬機器的螢幕擷取畫面](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升級您的配額
建立 Azure Batch 帳戶時，為帳戶佈建的計算核心數目限制為 20 個。 我們可能想要增加此限制的更快速的製作時間，因為您可以平行處理許多節點，最多的場景中的探查點數樂器工作負載。 您可以按一下 Azure Batch 入口網站頁面上的 [配額] 連結，然後按一下 [申請加大配額]，來申請加大配額：

![Azure 配額的螢幕擷取畫面頁面](media/azure-quotas.png)

## <a name="next-steps"></a>後續步驟
* 整合到專案樂器外掛程式您[Unity](unity-integration.md)或是[Unreal](unreal-integration.md)專案

