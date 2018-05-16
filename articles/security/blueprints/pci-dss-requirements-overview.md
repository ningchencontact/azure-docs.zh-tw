---
title: Azure 付款處理藍圖 - 高階概觀
description: Azure 付款處理藍圖 - 客戶責任矩陣 (概觀)
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>PCI DSS 需求 - 高階概觀

開發支付卡產業資料安全標準 (PCI DSS) 的目的是提升及加強持卡人資料安全性，並促進全球廣泛採用一致的資料安全性措施。 PCI DSS 提供保護帳戶資料技術和作業的需求基準。 PCI DSS 適用於所有涉及到支付卡處理的實體，包括商家、處理機構、收單機構、發行機構及服務提供者。 PCI DSS 也適用於所有其他儲存、處理或傳輸持卡人資料 (CHD) 及/或敏感驗證資料 (SAD) 的實體。 以下是 12 個 PCI DSS 需求的高階概觀。

> [!NOTE]
> 這些需求皆由[支付卡產業 (PCI) 安全標準委員會](https://www.pcisecuritystandards.org/pci_security/) \(英文\) 於 [PCI 資料安全標準 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss) \(英文\) 中所定義。 若要了解每個需求的測試程序和指導方針相關資訊，請參閱 PCI DSS。

|   |   |
|---|---|
| **建置與維護安全的<br/>網路和系統** | 1.[安裝及維護防火牆設定，以保護持卡人資料](pci-dss-requirement-1-firewall.md)<br/><br/> 2.[請勿使用廠商提供的預設值作為系統密碼和其他安全性參數](pci-dss-requirement-2-password.md) |  
| **保護持卡人資料** | 3.[保護儲存的持卡人資料](pci-dss-requirement-3-chd.md)<br/><br/> 4.[在開放的公用網路上傳輸持卡人資料時進行加密](pci-dss-requirement-4-encryption.md) |
| **弱點維修<br/>管理程式** | 5.[保護所有系統以防範惡意程式碼，並定期更新防毒軟體或程式](pci-dss-requirement-5-malware.md)<br/><br/> 6.[開發與維護安全的系統和應用程式](pci-dss-requirement-6-secure-system.md) |
| **實作強式存取<br/>控制量值** | 7.[限制讓業務上必須知道的人存取持卡人資料](pci-dss-requirement-7-access.md)<br/><br/> 8.[識別及驗證系統元件的存取](pci-dss-requirement-8-identity.md) <br/><br/> 9.[限制持卡人資料的實際存取](pci-dss-requirement-9-physical-access.md) |
| **定期監視和<br/>測試網路** | 10.[追蹤並監視針對網路資源與持卡人資料的所有存取](pci-dss-requirement-10-monitoring.md) <br/><br/> 11.[定期測試安全性系統和流程](pci-dss-requirement-11-testing.md) |
| **維護資訊<br/>安全性原則** | 12.[維護解決所有人員資訊安全性的原則](pci-dss-requirement-12-policy.md) |

