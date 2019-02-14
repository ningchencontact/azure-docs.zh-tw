---
title: 了解 Azure 成本管理資料 | Microsoft Docs
description: 本文將協助您深入了解 Azure 成本管理所包含的資料，及其處理、收集、顯示和關閉的頻率。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0531c106228190fdc40f494e8eee70ec550f6404
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820246"
---
# <a name="understand-cost-management-data"></a>了解成本管理資料

本文將協助您深入了解 Azure 成本管理所包含的資料。 此外也說明處理、收集、顯示和關閉資料的頻率。 您須按月支付使用 Azure 的費用。 不過，您的 Azure 訂用帳戶類型會決定您的計費月份於何時結束。 「成本管理」接收使用量資料的頻率會隨著不同的因素而改變。 這類因素包括處理資料所需的時間，以及 Azure 服務向計費系統發出使用量資料的頻率。

## <a name="supported-microsoft-offers"></a>支援的 Microsoft 供應項目

下列資訊顯示 Azure 成本管理中目前支援的 [Microsoft Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。  Azure 供應項目是您 Azure 訂用帳戶的類型。

| 類別  | **供應項目名稱** | **供應項目號碼** |
| --- | --- | --- |
| **Enterprise 合約 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Enterprise 合約 (EA)**| Enterprise 開發/測試 | MS-AZR-0148P |
| **Microsoft Developer Network (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |
| **直接/預付型方案** | [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **直接/預付型方案** | Microsoft Azure 內部使用 | MS-AZR-0015P |
| **直接/預付型方案** | [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **直接/預付型方案** | [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **直接/預付型方案** | [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **直接/預付型方案** | [免費試用版](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **直接/預付型方案** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **直接/預付型方案** | [Azure 學生版](https://azure.microsoft.com/en-us/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **直接/預付型方案** | Azure Pass | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P |

下表顯示不支援的供應項目。

| 類別  | **供應項目名稱** | **供應項目號碼** |
| --- | --- | --- |
| **雲端解決方案提供者 (CSP)** | Microsoft Azure | MS-AZR-0145P |
| **雲端解決方案提供者 (CSP)** | Azure Government CSP | MS-AZR-USGOV-0145P |
| **雲端解決方案提供者 (CSP)** | CSP 中適用於 Microsoft Cloud Germany 的 Azure Germany | MS-AZR-DE-0145P |
| **直接/預付型方案** | Azure 學生入門版 | MS-AZR-0144P |
| **支援方案** | 標準支援 | MS-AZR-0041P |
| **支援方案** | 專業指導支援 | MS-AZR-0042P |
| **支援方案** | 開發人員支援 | MS-AZR-0043P |
| **支援方案** | 德國支援方案 | MS-AZR-DE-0043P |
| **支援方案** | Azure Government 標準支援 | MS-AZR-USGOV-0041P |
| **支援方案** | Azure Government 專業指導支援 | MS-AZR-USGOV-0042P |
| **支援方案** | Azure Government 開發人員支援 | MS-AZR-USGOV-0043P |

如果您未看到訂用帳戶的資料，而您想要確認訂用帳戶是否屬於支援的供應項目，您可以驗證您的訂用帳戶是否受支援。 若要驗證 Azure 訂用帳戶是否受支援，請登入 [Azure 入口網站](https://portal.azure.com)。 然後，在左側功能表窗格中選取 [所有服務]。 在服務清單中，選取 [訂用帳戶]。 在訂用帳戶清單功能表中，按一下您要確認的的訂用帳戶。 您的訂用帳戶會顯示在 [概觀] 索引標籤上，您會看到 [供應項目] 和 [供應項目識別碼]。 下圖說明一個範例。

![顯示供應項目和供應項目識別碼的訂用帳戶概觀範例](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>包含在成本管理中的成本

下表顯示「成本管理」中包含或未包含的資料。

**帳戶類型**

| **包含** | **未包含** |
| --- | --- |
| Enterprise 合約 (EA) | 雲端解決方案提供者 (CSP) - 如需詳細資訊，請參閱[合作夥伴中心概觀](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。 |
| 預付型方案 (PAYG) |   |
| 開發/測試 |   |
| 免費、試用和贊助 |   |
| 合作夥伴網路 |   |
| Azure in Open | &nbsp;  |

**成本和使用量資料**

| **包含** | **未包含** |
| --- | --- |
| Azure 服務使用量<sup>1</sup> | 保留購買 – 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API](../billing/billing-reservation-apis.md)。 |
| Marketplace 供應項目使用量 | Marketplace 購買 – 如需詳細資訊，請參閱[第三方服務費用](../billing/billing-understand-your-azure-marketplace-charges.md)。 |
|   | 支援費用 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
|   | 稅金 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
|   | 點數 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |

<sup>1</sup> Azure 服務使用量以保留和議定價格為基礎。

**Metadata**

| **包含** | **未包含** |
| --- | --- |
| 資源標記<sup>2</sup> | 資源群組標記 |

<sup>2</sup> 每項服務發出使用量資料時即會套用資源標記，且其套用不會溯及歷史使用量。

## <a name="rated-usage-data-refresh-schedule"></a>評比使用量資料重新整理排程

成本與使用量資料可從 Azure 入口網站和[支援 API](https://aka.ms/costmgmt/docs) 的 [成本管理 + 計費] 中取得。 檢閱成本時，請記住以下幾點：

- 目前計費期間的預估費用每天會更新 6 次。
- 目前計費期間的預估費用可能隨著您產生更多使用量而變更。
- 每次更新將持續累積，包含前次更新的所有明細項目和資訊。
- Azure 會在計費期間結束後的 72 小時 (三個日曆天) 內終止或_關閉_目前計費期間。

下列範例說明計費期間的結束方式。

Enterprise 合約 (EA) 訂用帳戶 – 如果計費月份在 3 月 31 日結束，預估費用將會在 72 個小時內更新。 在此範例中，即 4 月 4 日午夜 (UTC)。

預付型方案訂用帳戶 – 如果計費月份在 5 月 15 日結束，預估費用將可在 72 個小時內更新。 在此範例中，即 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新評估的資料

無論您使用[成本管理 API](https://aka.ms/costmgmt/docs)、PowerBI 還是 Azure 入口網站來擷取資料，都應預期目前計費期間的費用可能進行重新評估，且持續變更，直到發票結算為止。

## <a name="usage-data-update-frequency-varies"></a>使用量資料更新頻率不相同

您產生的使用量資料在成本管理中的可用性取決於某些因素，包括：

- Azure 服務 (例如儲存體、計算、CDN 和 SQL) 發出使用量資料的頻率。
- 透過評比引擎和成本管理管線處理使用量資料所花費的時間。

某些服務會比其他服務更頻繁地發出使用量資料。 因此，您在「成本管理」中可能會看到某些服務的資料比其他發出資料頻率較低的服務更快出現。 服務的使用量通常需要 8-24 小時才會出現在「成本管理」中。 請記住，在尚未結算的月份中，該資料會隨著您產生更多使用量而重新整理，因為更新會持續累積。

## <a name="see-also"></a>另請參閱

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
