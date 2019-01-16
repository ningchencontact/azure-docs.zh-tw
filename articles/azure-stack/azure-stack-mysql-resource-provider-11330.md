---
title: Azure Stack MySQL 資源提供者 1.1.30.0 版本資訊 | Microsoft Docs
description: 了解最新的 Azure Stack MySQL 資源提供者更新 (包括任何已知問題)，以及更新的下載位置。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: 0e042f1ac1b70b13a6b95ae9cfc1269774ecdb44
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2019
ms.locfileid: "54155094"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL 資源提供者 1.1.33.0 版本資訊

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

這些版本資訊說明 MySQL 資源提供者 1.1.33.0 版的改進和已知問題。

## <a name="build-reference"></a>建置參考
下載 MySQL 資源提供者二進位檔，然後執行自我解壓縮程式，以將內容解壓縮到至暫存目錄。 資源提供者會有最低限度的相對應 Azure Stack 組建。 要安裝此版本的 MySQL 資源提供者所需的最低 Azure Stack 發行版本如下所列：

> |最低 Azure Stack 版本|MySQL 資源提供者版本|
> |-----|-----|
> |1808 版 (1.1808.0.97)|[MySQL RP 1.1.33.0 版](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版的 MySQL 資源提供者之前，請先將支援的最低 Azure Stack 更新套用到您的 Azure Stack 整合式系統，或部署最新的 Azure Stack 開發套件 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修正
此版本的 Azure Stack MySQL 資源提供者包含下列改良功能與修正：

### <a name="fixes"></a>修正
- **MySQL 資源提供者入口網站擴充功能可能會選擇錯誤的訂用帳戶**。 MySQL 資源提供者會使用 Azure Resource Manager 呼叫來判斷要第一個使用的服務管理員訂用帳戶，但這可能不是「預設的提供者訂用帳戶」。 如果發生這種情況，MySQL 資源提供者將無法正常運作。 

- **MySQL 裝載伺服器不會列出裝載的資料庫。** 在檢視 MySQL 裝載伺服器的租用戶資源時，可能不會列出使用者建立的資料庫。

- **如果未啟用 TLS 1.2，之前的 MySQL 資源提供者 (1.1.30.0) 部署可能會失敗**。 將 MySQL 資源提供者更新為 1.1.33.0，以在部署資源提供者、更新資源提供者或輪替祕密時啟用 TLS 1.2。 

- **MySQL 資源提供者的祕密輪替失敗**。 已修正會在輪替祕密時導致下列錯誤碼的問題：` New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>已知問題 

- **最多需要一小時才能在入口網站中看到 MySQL SKU**。 在建立新的 MySQL 資料庫時，新建立的 SKU 最多需要一小時才會顯示以供使用。 

    **因應措施**：無。

- **重複使用的 MySQL 登入**。 嘗試使用與現有登入相同的使用者名稱在相同的訂用帳戶下建立新的 MySQL 登入時，會導致重複使用相同登入和現有密碼的問題。 

    **因應措施**：在相同的訂用帳戶下，建立新登入時請使用不同的使用者名稱；或在不同訂用帳戶下，使用相同的使用者名稱建立登入。

- **共用的 MySQL 登入導致資料不一致**。 如果在相同的訂用帳戶下讓多個 MySQL 資料庫共用一個 MySQL 登入，變更登入密碼將會導致資料不一致。

    **因應措施**：在相同的訂用帳戶下，一律讓不同的資料庫使用不同的登入。


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>雲端管理員操作 Azure Stack 時的已知問題
請參閱 [Azure Stack 版本資訊](azure-stack-servicing-policy.md)中的文件。

## <a name="next-steps"></a>後續步驟
[深入了解 MySQL 資源提供者](azure-stack-mysql-resource-provider.md)。

[準備部署 MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[從舊版升級 MySQL 資源提供者](azure-stack-mysql-resource-provider-update.md)。 