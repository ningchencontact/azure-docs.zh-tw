---
title: 聲場專案 Azure Batch 帳戶設定
titlesuffix: Azure Cognitive Services
description: 本 how to 說明如何設定 Azure Batch 帳戶, 以搭配聲場專案 Unity 和 Unreal engine 整合使用。
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: f11dfda62ebb53aba6254f2db4eace7c524141d4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704887"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>聲場專案 Azure Batch 帳戶設定
本 how to 說明如何設定 Azure Batch 帳戶, 以搭配聲場專案 Unity 和 Unreal engine 整合使用。

## <a name="get-an-azure-subscription"></a>取得 Azure 訂用帳戶
您必須先有 [Azure 訂用帳戶](https://azure.microsoft.com/free/)，才能設定 Batch 與「儲存體」帳戶。 如果您是第一次註冊，Azure 會提供一些有時間限制的免費資源與 200 美元的信用額度。

## <a name="create-azure-batch-and-storage-accounts"></a>建立 Azure Batch 與儲存體帳戶
接著，請依照[這些指示](https://docs.microsoft.com/azure/batch/batch-account-create-portal)來設定 Azure Batch 和相關的「Azure 儲存體」帳戶。

為 Batch 與「儲存體」帳戶選取預設選項：
  
  ![顯示預設設定的 Azure Batch 新帳戶選項的螢幕擷取畫面](media/new-batch-account-create.png)

  ![顯示預設設定的 Azure 儲存體新帳戶選項的螢幕擷取畫面](media/batch-storage-account-create.png)

Azure 需要幾分鐘的時間來部署帳戶。 在入口網站的右上角即可找到完整的通知。
  
  ![Azure 帳戶已部署通知的螢幕擷取畫面](media/batch-accounts-deploy-notification.png)

您現在應該可以在儀表板上看到您的帳戶。
  
  ![顯示 Batch 和儲存體帳戶之 Azure 入口網站儀表板的螢幕擷取畫面](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 認證來設定聲場製作 UI
按一下儀表板上的 Batch 帳戶連結，然後按一下 Batch 帳戶頁面上的 [金鑰] 連結以存取您的認證。
  
  ![已反白顯示 [金鑰連結] 頁面的 Azure Batch 帳戶螢幕擷取畫面](media/batch-access-keys.png)

  ![具有存取金鑰的 Azure Batch 帳戶金鑰 頁面的螢幕擷取畫面](media/batch-keys-info.png)

按一下頁面上的 [儲存體帳戶] 連結，以存取您的「Azure 儲存體」帳戶認證。
  
  ![具有存取金鑰的 Azure 儲存體帳戶金鑰 頁面的螢幕擷取畫面](media/storage-keys-info.png)

在[Unity 製作外掛程式](unity-baking.md)或[Unreal 製作外掛程式](unreal-baking.md)中輸入這些認證。

## <a name="node-types-and-region-support"></a>節點類型與區域支援
聲場專案需要 Fsv2 和 H 系列計算優化的 Azure VM 節點, 但可能不會在所有 Azure 區域中受到支援。 請參閱[此表格](https://azure.microsoft.com/global-infrastructure/services)，以確保您為 Batch 帳戶選取的位置正確。
![依區域顯示 Azure 虛擬機器的螢幕擷取畫面](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升級您的配額
建立 Azure Batch 帳戶時，為帳戶佈建的計算核心數目限制為 20 個。 我們可能會想要增加此限制, 以加快製作時間, 因為您可以將聲場工作負載平行處理到許多節點, 最多可達場景中的探查點數目。 您可以按一下 Azure Batch 入口網站頁面上的 [配額] 連結，然後按一下 [申請加大配額]，來申請加大配額：

![Azure 配額頁面的螢幕擷取畫面](media/azure-quotas.png)

## <a name="next-steps"></a>後續步驟
* 將聲場專案外掛程式整合到您的[Unity](unity-integration.md)或[Unreal](unreal-integration.md)專案

