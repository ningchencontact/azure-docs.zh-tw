---
title: 如何取得 Avere vFXT for Azure 的支援
description: 開立有關 Avere vFXT for Azure 支援票證的說明
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 068747fd51779bd8a20636355a9ce2b032f6151d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255370"
---
# <a name="get-help-with-your-system"></a>取得有關系統的協助

如果您需要有關 Avere vFXT for Azure 的協助，以下是取得支援的各種方式：

* **Avere vFXT 問題** - 使用 Azure 入口網站，為 Avere vFXT 開立支援票證，如[下方](#open-a-support-ticket-for-your-avere-vfxt)所述。
* **配額** - 如果您有與配額相關的問題，請[要求增加配額](#request-a-quota-increase)
* **文件和範例** - 如果您發現本文件或範例中有任何問題，請捲動到存在問題的頁面底部，並使用 [意見反應] 區段搜尋現有的問題，如果需要，提出一個新問題。  

## <a name="open-a-support-ticket-for-your-avere-vfxt"></a>為 Avere vFXT 開立支援票證

如果您在部署或使用 Avere vFXT 時遇到問題，請透過 Azure 入口網站要求協助。  

請遵循下列步驟，確定您的支援票證已使用您叢集中的資源標記。 標記票證可協助我們將其發送至正確的技術資源。 

1. 從 [https://portal.azure.com](https://portal.azure.com) 選取 [資源群組]。

   ![Azure 入口網站左側功能表的螢幕擷取畫面，其中圈出「資源群組」](media/avere-vfxt-ticket-rg.png)

1. 瀏覽至包含發生問題所在 vFXT 叢集的資源群組，然後按一下其中一部 Avere 虛擬機器。

    ![Azure 入口網站資源群組「概觀」面板的螢幕擷取畫面，其中圈出特定的 VM](media/avere-vfxt-ticket-vm.png)

1. 在 [VM] 頁面中，向下捲動至左側面板下方，然後按一下 [新增支援要求]。

    ![上一個螢幕擷取畫面中 VM 的 Azure 入口網站 VM 頁面的螢幕擷取畫面。 左側功能表捲動至底部，並圈出「新增支援要求」。](media/avere-vfxt-ticket-request.png)

1. 在支援要求的第一頁上，按一下 [所有服務]，然後查看 [儲存體] 底下，選擇 [Avere vFXT]。

    ![Azure 入口網站中新增支援要求畫面的螢幕擷取畫面，其中的標頭為「基本」並圈出「服務」項目。 選取底部的「所有服務」，且下拉式功能表欄位的值為「Avere vFXT」](media/avere-vfxt-ticket-service.png)

1. 在第二頁上，選擇最符合您問題的問題類型和類別。 新增簡短標題，以及包含問題發生時間的描述。 

   ![標頭為「問題」之新增支援要求畫面的螢幕擷取畫面，其中包含必須完成的許多欄位](media/avere-vfxt-ticket-problem.png)

1. 在第三頁上，填入您的連絡資訊，然後按一下 [建立]。 確認與票證編號將會傳送至您的電子郵件地址，且支援人員成員將會與您連絡。

## <a name="request-a-quota-increase"></a>要求增加配額

若要了解部署 Avere vFXT for Azure 所需的元件，請閱讀 [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)。 您可以從 Azure 入口網站[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。