---
title: 以 STIG 為基礎的設定，以用於狀態設定-Azure 自動化
description: 根據 Azure 自動化中的狀態設定，瞭解以 STIG 為基礎的設定。
keywords: dsc，powershell，設定，安裝程式
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243535"
---
# <a name="configuration-based-on-stig"></a>以 STIG 為基礎的設定

> 適用於：Windows PowerShell 5.1

第一次建立設定內容可能是一項挑戰。
在許多情況下，目標是要以「基準」來自動設定伺服器，以符合業界建議。

> [!NOTE]
> 本文是指由開放原始碼社區維護的解決方案。
> 支援僅以 GitHub 共同作業的形式提供，而不是來自 Microsoft。

## <a name="community-project-powerstig"></a>社區專案：PowerSTIG

名為[PowerSTIG](https://github.com/microsoft/powerstig)的社區專案的目標是要解決這個問題，方法是根據所提供有關 STIG 的[公用資訊](https://public.cyber.mil/stigs/)產生 DSC 內容（安全性技術實施指南），

處理基準比聽起來更為複雜。
許多組織都需要記載規則的[例外](https://github.com/microsoft/powerstig#powerstigdata)狀況，並大規模管理該資料。
PowerSTIG 藉由提供[複合資源](https://github.com/microsoft/powerstig#powerstigdsc)來處理每個設定區域，而不是嘗試在一個大型檔案中處理整個範圍的設定，來解決此問題。

一旦產生設定之後，您就可以使用 DSC 設定[腳本](/powershell/scripting/dsc/configurations/configurations)來產生 mof 檔案，並將[mof 檔案上傳到 Azure 自動化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然後從[內部部署](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)註冊您的伺服器，以提取設定。

若要試用 PowerSTIG，請造訪[PowerShell 資源庫](http://www.powershellgallery.com)並下載解決方案，或按一下 [專案網站] 以查看[檔](https://github.com/microsoft/powerstig)。

## <a name="next-steps"></a>後續步驟

- [Windows PowerShell 預期狀態設定概觀](/powershell/dsc/overview/overview)
- [DSC 資源](/powershell/dsc/resources/resources)
- [設定本機 Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
