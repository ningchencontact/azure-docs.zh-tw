---
title: Azure Stack 服務原則 | Microsoft Docs
description: 深入了解 Azure Stack 服務原則，以及如何將整合的系統保持在支援的狀態。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 8abbf3a9db02fda0c92485ef75a12d82f4e8c8e9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903942"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服務原則

本文說明 Azure Stack 整合系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態。

## <a name="download-update-packages-for-integrated-systems"></a>下載適用於整合式系統的更新套件類型

Microsoft 除了發行 Hotfix 套件來解決特定問題之外，也會發行完整的每月更新套件。

每月更新套件裝載在安全的 Azure 端點中。 您可以使用 [Azure Stack 更新下載程式工具](https://aka.ms/azurestackupdatedownload)來下載它們。 如果您的縮放單位已連線，更新就會自動在系統管理員入口網站中顯示為「已有可用的更新」。 完整的每月更新套件會詳細記載於每個版本。 如需有關每個版本的詳細資訊，您可以從本文的[更新套件發行頻率](#update-package-release-cadence)一節中按一下任何一個版本。

Hotfix 更新套件裝載在相同的安全 Azure 端點中。 您可以使用個別 Hotfix 知識庫文章中內嵌的連結 (例如 [Azure Stack Hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114)) 來手動下載它們。 與完整的每月更新套件類似，Azure Stack 操作員可以下載 .xml、.bin 及 .exe 檔案，然後使用[在 Azure Stack 中套用更新](azure-stack-apply-updates.md)中的程序來匯入這些檔案。 Azure Stack 操作員如果具有已連線的縮放單位，就會看到 Hotfix 自動出現在系統管理員入口網站中並顯示「已有可用的更新」訊息。

如果您的縮放單位未連線，而您想要收到每個 Hotfix 版本的通知，請訂閱每個版本中註明的 [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) 或 [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) 摘要。  

## <a name="update-package-types"></a>更新封裝類型

整合系統有兩種更新套件類型：

- **Microsoft 軟體更新**。 Microsoft 會負責 Microsoft 軟體更新封裝的端對端服務生命週期。 這些封裝包含最新的 Windows Server 安全性更新、非安全性更新，以及 Azure Stack 功能更新。 您可以直接從 Microsoft 下載這些更新封裝。

- **OEM 硬體廠商提供的更新**。 Azure Stack 硬體合作夥伴負責硬體相關韌體和驅動程式更新封裝的端對端服務生命週期 (包括指引)。 此外，對於硬體生命週期主機上的所有軟體和硬體，Azure Stack 硬體合作夥伴擁有並維護指引。 OEM 硬體廠商在自己的下載網站提供這些更新封裝。

## <a name="update-package-release-cadence"></a>更新封裝發行頻率

Microsoft 預期每月發行軟體更新封裝。 不過，可能一個月內發行多個更新或沒有任何更新。 OEM 硬體廠商會視需要發行更新。

在[管理更新概觀](azure-stack-updates.md)文件中可找到如何規劃和管理更新，以及如何判斷您目前的版本。

如需特定更新的詳細資訊，包括如何下載更新，請參閱該更新的版本資訊：

- [Azure Stack 1903 更新](azure-stack-update-1903.md)
- [Azure Stack 1902 更新](azure-stack-update-1902.md)
- [Azure Stack 1901 更新](azure-stack-update-1901.md)
- [Azure Stack 1811 更新](azure-stack-update-1811.md)

## <a name="hotfixes"></a>Hotfix

有時候，Microsoft 會為 Azure Stack 提供 Hotfix，通常可解決預防性或時效性的特定問題。  每個 Hotfix 都會隨著對應的 Microsoft 知識庫文章發行，文中詳述問題、原因和解決方案。

下載和安裝 Hotfix 就像 Azure Stack 的一般完整更新套件。 不過，不同於完整的更新，Hotfix 可在數分鐘內安裝完畢。 建議 Azure Stack 操作員在安裝 Hotfix 時設定維護期間。 Hotfix 會更新 Azure Stack 雲端的版本，因此您可以輕鬆地判斷是否已套用 Hotfix。 每個仍受支援的 Azure Stack 版本都會有個別提供的 Hotfix。 每個特定反覆項目的修正都是累計的，而且包含該相同版本的先前更新。 您可以在修正的對應知識庫中閱讀特定 Hotfix 的適用性相關資訊。  

## <a name="keep-your-system-under-support"></a>維持系統獲得支援

若要繼續獲得支援，您必須將 Azure Stack 部署維持在最新狀態。 更新的延期原則是：若要讓 Azure Stack 部署持續獲得支援，它必須執行最新發行的更新版本，或是執行最新版之前的兩個更新版本其中之一。 Hotfix 並不被視為主要更新版本。 如果您的 Azure Stack 雲端落後*不只兩個更新*，就會被視為不符合規範，而必須更新至最低支援版本才能獲得支援。

例如，如果最新可用更新版本是 1805，而前兩個更新套件的版本為 1804 和 1803，則 1804 和 1803 都可持續獲得支援。 不過，1802 則不在支援之列。 此原則在持續一或兩個月沒有任何發行版本的情況下仍然成立。 例如，如果最新發行版本為 1805，但並沒有 1804 發行版本，則前兩個更新套件 1803 和 1802 會持續獲得支援。

Microsoft 軟體更新套件不會累積，而且先決條件是必須要有上一個更新套件或 Hotfix。 如果您決定延後一或多個更新，但是想要取得最新版本，請考慮整體的執行時間。

## <a name="get-support"></a>取得支援

Azure Stack 會遵循和 Azure 相同的支援程序。 企業客戶可以依照[如何建立 Azure 支援要求](/azure/azure-supportability/how-to-create-azure-support-request)中所述的程序進行。 如果您是雲端服務提供者 (CSP) 的客戶，請連絡您的 CSP 以尋求支援。  如需詳細資訊，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟

- [管理 Azure Stack 中的更新](azure-stack-updates.md)