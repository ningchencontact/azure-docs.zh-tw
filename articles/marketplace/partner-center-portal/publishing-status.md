---
title: 檢查發佈狀態的商業的 Marketplace 供應項目
description: 在 Microsoft 合作夥伴中心內發佈商業的 marketplace 供應項目所需的驗證、 憑證及預覽步驟的狀態檢查。
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ae4840877ae461244a76984ed8ac3edc82d57a38
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653988"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>檢查發佈狀態的商業的 Marketplace 供應項目

您可以檢視您目前**發佈狀態**上**提供概觀**索引標籤[商業 Marketplace 入口網站](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)在合作夥伴中心。

每個供應項目應該都會顯示下列狀態指標的其中一個。

| **狀態**    | **說明**  |
| :---------- | :-------------------|
| **Draft**(英文\) | 已建立供應項目，但是它不正在發行。 |
| **正在發行** | 供應項目/計劃開始一路逐步完成發行程序。 |
| **需要注意** | 嚴重的問題在憑證期間探索到的 Microsoft 或其任何發佈的步驟。 |
| **預覽** | 供應項目已由 Microsoft 認證，並立即等候最終的驗證由 「 發行者 」。 若要讓供應項目選取上線 live。 |
| **Live** | 供應項目在 marketplace 中已上線並可以看到，取得客戶。 |
| **暫止的停止銷售** | 發行者的供應項目或方案，選取 「 停止銷售 」，但尚未完成的動作。 |
| **在 marketplace 中無法使用** | 已移除先前發佈的供應項目/計劃在 marketplace 中。 |

## <a name="automated-validation"></a>自動化的驗證

發佈程序的第一個步驟是一份自動驗證。 每個驗證步驟對應至您選擇要在建立您的供應項目中啟用的功能。 如果未啟用該功能，驗證會跳至下一個發佈的步驟。 發佈狀態為已核准之前，必須先完成每個驗證檢查。

- **提供採購流程安裝程式 (< 10 分鐘)**

在此步驟中，我們會確保客戶透過 Azure 入口網站購買時，可以滿足您的供應項目。 此步驟才適用的銷售，透過 Microsoft 的供應項目。

- **測試磁碟機資料驗證 （~ 5 分鐘）**

在此步驟中，我們會驗證您提供試用產品的供應項目技術的組態區段中的資料。 測試磁碟機功能是測試與核准。 使用試用產品啟用，才適用供應項目的這個步驟。

- **測試磁碟機佈建 （~ 30 分鐘）**

在此步驟中，驗證的資料和功能測試磁碟機在上一個步驟中之後, 我們會部署及複寫您的試用產品的執行個體，使其可供客戶使用。  使用試用產品啟用，才適用供應項目的這個步驟。

- **潛在客戶管理驗證和註冊 (< 15 分鐘)**

在此步驟中，我們可以確認您的潛在客戶管理系統可以收到根據提供的供應項目設定的詳細資料的潛在客戶。 這是步驟只適用於供應項目且已啟用潛在客戶管理。

## <a name="certification"></a>認證

在發佈之前必須經過認證提交給廣告在合作夥伴中心 Marketplace 的供應項目。 提交供應項目進行嚴格的測試，一些自動化和手動，包括檢查，以防止其他人[Azure Marketplace 認證原則](https://docs.microsoft.com/legal/marketplace/general-policies)。 提供提交必須標記為適合憑證才能進行下一個步驟中發佈的流程。

### <a name="types-of-validation-that-take-place-during-certification"></a>憑證期間進行的驗證類型

有三個層級的認證程序提交的每個供應項目中包含的驗證。

- 發行者商務資格
- 內容驗證
- 技術驗證

#### <a name="publisher-business-eligibility"></a>發行者商務資格

每個供應項目類型會檢查一組 「 發行者 」 必須符合的基底的資格準則。 適用性準則可能包括 「 發行者 」 的 MPN 狀態、 保留的專長認證，專長認證層級等。

#### <a name="content-validation"></a>內容驗證

在內容驗證期間建立您的供應項目時所輸入的資訊會檢查品質和相關性。 這些檢查會檢閱您的 marketplace 清單的詳細資訊，定價，可用性、 相關聯的計劃等的項目。為了符合 Azure Marketplace 和/或 AppSource 列出準則，我們會驗證您的供應項目包含：

- 精確地說明 供應項目; 標題
- 編寫完善的描述，提供完整的概觀和價值主張;
- 品質螢幕擷取畫面和隨附的影片，和
- 說明的供應項目如何利用 Microsoft 平台和工具。

相關內容的驗證準則閱讀深入[一般列出原則](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general-policies)。

#### <a name="technical-validation"></a>技術驗證

技術在驗證期間，供應項目 （套件或二進位），就會進行下列檢查。
- 掃描有惡意程式碼
- 受監視的網路呼叫
- 分析的封裝
- 供應項目的實際功能的完整掃描

跨各種平台和版本測試供應項目，是為了確保更為強固。

檢閱這份文件的技術的組態區段將供應項目所需的特定組態詳細資料。

### <a name="certification-failure-report"></a>憑證失敗報告

完成時檢閱，如果您的供應項目已跳過憑證然後它會沿著移動下一個步驟中發佈的程序。 如果您的供應項目失敗的任何清單、 技術和原則檢查，或如果您不提出該類型的供應項目時，憑證失敗報告產生，並以電子郵件傳送給您。

此報表包含任何失敗，以及檢閱附註的原則的描述。 檢閱此電子郵件報告，請解決任何問題，對您的供應項目進行更新，有需要然後重新提交供應項目使用[商業 Marketplace 入口網站](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)在合作夥伴中心。 （您可以重新提交供應項目，直到傳遞憑證所需的次數）。

## <a name="preview-creation"></a>預覽建立

期間**預覽建立**步驟中，我們建立版本的供應項目可存取您在 [預覽] 區段，您的供應項目中指定的對象。

## <a name="publisher-approval"></a>發行者核准

在此步驟中，您都會收到電子郵件並要求您檢閱並核准您的供應項目預覽，才能繼續的最後一個發佈的步驟。

如果您已選取銷售您的供應項目，透過 Microsoft，您將能夠測試的擷取和部署您的供應項目，以確保其符合您的需求，在此預覽核准階段中。 您的供應項目將尚無法使用公用 marketplace 中。 一旦您測試並核准此預覽版本，您必須選取**Go Live**上[**提供概觀**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)儀表板。

如果您想要在此預覽階段期間的供應項目進行變更，您可以編輯，然後發佈新的預覽重新提交。 請參閱文章[更新現有的 marketplace 供應項目](#update-existing-marketplace-offers)如需更多的變更詳細資料。

如果您的供應項目已即時與 marketplace 中公開上市，任何您所進行的更新不會上線，直到您選取**Go live**上[**提供概觀**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)儀表板。

### <a name="publish-offer-to-the-public"></a>在公開發行供應項目

登入合作夥伴中心，並存取供應項目。 您將會重新導向至**提供概觀**頁面。 在此頁面頂端，您會看到的選項**上線**。 選取 **上線，** ，確認之後，該供應項目便會開始取得公開發行。 即時供應項目時，您會收到電子郵件通知。

## <a name="publish"></a>發佈

既然您已選取**上線**您供應項目，將它提供在 marketplace 中，有一系列的最終驗證檢查，將會逐步執行，以確保即時的供應項目已設定一樣預覽版的產品。

- **提供採購流程安裝程式 (> 10 分鐘)**

在此步驟中，我們會確保客戶透過 Azure 入口網站購買時，可以滿足您的供應項目。 此步驟才適用的銷售，透過 Microsoft 的供應項目。

- **測試磁碟機資料驗證 （~ 5 分鐘）**

在此步驟中，我們會驗證您提供試用產品的供應項目技術的組態區段中的資料。 測試磁碟機功能是測試與核准。 使用試用產品啟用，才適用供應項目的這個步驟。

- **測試磁碟機佈建 （~ 30 分鐘）**

在此步驟中，我們會部署及複寫您的試用產品的執行個體，使其可供客戶使用。  使用試用產品啟用，才適用供應項目的這個步驟。

- **潛在客戶管理驗證和註冊 (> 15 分鐘)**

在此步驟中，我們可以確認您的潛在客戶管理系統可以收到根據提供的供應項目設定的詳細資料的潛在客戶。 這是步驟只適用於供應項目且已啟用潛在客戶管理。

- **最終發行 (> 30 分鐘)**

在此步驟中，我們會確保您的供應項目在 marketplace 中變為公開可用。

## <a name="update-existing-marketplace-offers"></a>更新現有的 marketplace 供應項目

如果您想要進行變更，您已發佈的供應項目，您必須先更新現有的供應項目，然後再重新發佈。

## <a name="next-steps"></a>後續步驟

- [更新現有商業的 marketplace 供應項目](./update-existing-offer.md)
