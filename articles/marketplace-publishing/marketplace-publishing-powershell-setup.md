---
title: 設定 PowerShell 以建立 Marketplace 的 VM | Microsoft Docs
description: 設定 Azure PowerShell 的指示，可做為選擇性處理流程來建立要部署至 Azure Marketplace 並在其上銷售的 VM 映像
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937394"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>設定 Azure PowerShell 以在 Azure Marketplace 上建立供應項目
如需如何設定 Azure PowerShell 的詳細資訊，請參閱 [如何安裝及設定 Azure PowerShell](/powershell/azure/overview)。 簡單的方式就是使用憑證方法，該方法會下載並匯入驗證所需的憑證。 若要取得所需的憑證，請使用 **Get-AzurePublishSettingsFile** Cmdlet。 出現系統提示時儲存檔案。 若要將憑證匯入 PowerShell 工作階段，請使用 **Import-AzurePublishSettingsFile** Cmdlet。

若要設定和儲存 PowerShell 工作階段的常見 Microsoft Azure 訂用帳戶設定，請使用 **Set-AzureSubscription** 和 **Select-AzureSubscription** Cmdlet：

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

第一個命令會讓預設儲存體帳戶與訂用帳戶 (有些 VM 佈建作業所需) 產生關聯。  第二個命令會讓訂用帳戶成為目前的訂用帳戶 (由其他 Cmdlet 辨識)。

## <a name="see-also"></a>另請參閱
* [使用者入門：如何將供應項目發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)
* [建立 Marketplace 的虛擬機器映像](marketplace-publishing-vm-image-creation.md)

