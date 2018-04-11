---
title: Azure Stack 服務原則 | Microsoft Docs
description: 深入了解 Azure Stack 服務原則，以及如何將整合的系統保持在支援的狀態。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.openlocfilehash: 2c10dcf185c62f3672be80ad2e3d049eae82fe6b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服務原則
本文說明 Azure Stack 整合系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態。 

## <a name="update-package-types"></a>更新封裝類型

整合的系統有兩種更新封裝；Microsoft 軟體更新，以及原始設備製造商 (OEM) 硬體廠商專屬的更新，例如驅動程式與韌體。 這些更新是以個別的 Azure Stack 更新封裝提供，而且獨立管理。

- **Microsoft 軟體更新**。 Microsoft 會負責 Microsoft 軟體更新封裝的端對端服務生命週期。 這些封裝包含最新的 Windows Server 安全性更新、非安全性更新，以及 Azure Stack 功能更新。 您可以直接從 Microsoft 下載這些更新封裝。
- **OEM 硬體廠商提供的更新**。 Azure Stack 硬體合作夥伴負責硬體相關韌體和驅動程式更新封裝的端對端服務生命週期 (包括指引)。 此外，對於硬體生命週期主機上的所有軟體和硬體，Azure Stack 硬體合作夥伴擁有並維護指引。 OEM 硬體廠商在自己的下載網站提供這些更新封裝。

## <a name="update-package-release-cadence"></a>更新封裝發行頻率

Microsoft 預期每月發行軟體更新封裝。 不過，可能一個月內發行多個更新或沒有任何更新。 OEM 硬體廠商會視需要發行更新。

Microsoft 更新封裝包含下列命名慣例，可協助您輕鬆識別發行日期：

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

例如，2017 年 6 月 15 日發行的 Microsoft 軟體更新是版本「1.0.170615.1」。

## <a name="keep-your-system-under-support"></a>維持系統獲得支援
若要繼續獲得支援，您必須將 Azure Stack 部署維持在最新狀態。 延期更新原則的規定是，若要讓 Azure Stack 持續獲得支援，它必須執行最新發行的更新版本，或是執行最新版之前的兩個主要更新版本其中之一。  Hotfix 並不被視為主要更新版本。  如果您的 Azure Stack 雲端落後*不只兩個更新*，就會被視為不符合規範，而必須更新至最低支援版本才能獲得支援。 

例如，如果最新可用更新版本是 1805，而前兩個更新套件的版本為 1804 和 1803，則 1804 和 1803 都可持續獲得支援。 不過，1802 則不在支援之列。 此原則在持續一或兩個月沒有任何發行版本的情況下仍然成立。 例如，如果最新發行版本為 1805，但並沒有 1804 發行版本，則前兩個更新套件 1803 和 1802 將持續獲得支援。

Microsoft 軟體更新套件不會累積，而且先決條件是必須要有上一個更新套件。 如果您決定延後一或多個更新，但是想要取得最新版本，請考慮整體的執行時間。 

下表顯示範例更新封裝發行、其先決條件，以及獲得支援必要的系統最低支援版本。 該表是以 Azure Stack 整合系統 (組建 1708) 為依據，第一次更新封裝發行 (1709) 是在 2017 年 9 月。 

| 最新更新封裝 (*範例*) | 必要條件 | 最低支援版本 |
| -- | -- | -- |
| 1710 | 1709 | N/A |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1712 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 
在上表中，沒有 1801 發行版本。

## <a name="next-steps"></a>後續步驟

- [在 Azure Stack 中管理更新](azure-stack-updates.md)


