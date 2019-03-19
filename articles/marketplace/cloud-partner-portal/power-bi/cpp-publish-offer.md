---
title: 發佈 Power BI 應用程式供應項目 - Azure Marketplace | Microsoft Docs
description: 發佈 Microsoft AppSource marketplace 上的 Power BI 應用程式供應項目。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822747"
---
# <a name="publish-a-power-bi-app-offer"></a>發佈 Power BI 應用程式供應項目

最後一個步驟中，當您在 Cloud Partner 入口網站中定義供應項目並建立相關聯的技術資產之後, 是提交進行發佈供應項目。 若要啟動此程序，在左窗格中**新增供應項目**視窗中，選取**發佈**。 如需詳細資訊，請參閱[發佈 Azure Marketplace 和 AppSource 供應項目](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>發佈步驟

以下是在發佈程序中的主要步驟：

![提供 Power BI 應用程式的發佈程序步驟](./media/publishing-process-steps.png)

下表描述每個步驟，並提供估計的完成時間：

|   發行步驟            |   時間     |   描述                                                                  |
| --------------------         |------------| ----------------                                                               |
| 檢查必要條件       | 15 分鐘     | 檢查供應項目資訊和供應項目設定。                            |
| 認證                | 1-7 天   | Power BI 認證團隊會分析您的供應項目。 小組安裝的應用程式，透過提供的安裝 URL，透過手動驗證測試執行您的 Power BI 應用程式。 （在本文件稍後所述） 的應用程式認證程序的一部分，會執行主要驗證。         |
| 包裝中                    | \< 1 小時  | 供應項目的技術資產封裝供客戶使用。                        |
| 會導致產生註冊 | \< 1 小時  | 設定和部署潛在客戶系統。                                      |
| 發行者簽核            | \-         | 供應項目上線之前完成最終檢閱和確認。 您現在也必須連結以預覽您的供應項目。 您滿意預覽的外觀之後，請選取**Go Live**上**狀態** 索引標籤。這是要列出您在 AppSource 上的應用程式上架小組傳送要求。    |
| 即時                         | \< 3 小時 | 供應項目現在會公開列 （「 即時 」） 於 AppSource，客戶可以檢視您的應用程式，並將它部署在其 Power BI 訂用帳戶中。 您也會收到確認電子郵件。 在右側的欄**所有供應項目**索引標籤上，您可以看到所有供應項目的狀態。 在 **狀態**索引標籤上，您可以看到您的供應項目的詳細發佈流程狀態。 |
|   |   |

允許最多八天才能完成此程序。 您瀏覽這些發佈的步驟之後，您的 Power BI 應用程式供應項目將會列在[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 應用程式 區段。


### <a name="app-certification-process"></a>應用程式認證流程

Microsoft 上架小組會使用此程序來驗證您的 Power BI 應用程式供應項目提交：

1. 檢閱法律文件和說明連結。
2. 驗證的支援連絡資訊。
3. 使用安裝程式的 URL 來確認適當的安裝。
4. 掃描惡意程式碼和其他惡意內容的應用程式。
5. 確認顯示的內容符合應用程式的描述。
6. 請確認應用程式相關的作業運作如預期般在 Power BI 中。 小組會開啟報表和儀表板的範例資料，連接到自訂資料來源，重新整理資料，並依此類推。

如果認證小組發現任何問題，他們會提供意見反應。  如需有關 Power BI 應用程式需求的詳細資訊，請參閱 < [Power BI 應用程式文件](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>後續步驟

我們建議您定期監視您的應用程式中[AppSource marketplace](https://appsource.microsoft.com)。  您也應該使用[Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md)功能[Cloud Partner 入口網站](https://cloudpartner.azure.com/#insights)若要深入了解您的 marketplace 客戶和應用程式使用量。 最後，您可以[更新您的供應項目](./cpp-update-existing-offer.md)。
