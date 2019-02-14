---
title: 發佈 Power BI 應用程式供應項目 - Azure Marketplace | Microsoft Docs
description: 在 Microsoft AppSource Marketplace 上發佈 Power BI 應用程式供應項目。
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
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665709"
---
# <a name="publish-power-bi-app-offer"></a>發佈 Power BI 應用程式供應項目

在入口網站設定供應項目，並建立相關技術資產後，最後一步就是提交供應項目以供發佈。  若要啟動此程序，請在 [新增供應項目] 視窗中按一下垂直功能表上的 [發佈] 按鈕。  如需詳細資訊，請參閱[發佈 Azure Marketplace 和 AppSource 供應項目](../manage-offers/cpp-publish-offer.md)。


## <a name="publishing-steps"></a>發佈步驟

下圖說明「上線」發佈流程中的主要步驟。

![適用於 Power BI 應用程式的發佈流程步驟](./media/publishing-process-steps.png)

下表說明這些步驟，並提供完成所需預估最多時間：

|   發行步驟            |   時間     |   說明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 檢查必要條件       | 15 分鐘     | 檢查供應項目資訊和供應項目設定。                            |
| 認證                | 1-7 天   | Power BI 認證小組會分析您的供應項目。 我們會透過您所提供的安裝 URL 安裝應用程式，然後執行您的 Power BI 應用程式以進行手動驗證測試。 主要驗證會在應用程式認證流程中執行。如下所示。         |
| 包裝中                    | \< 1 小時  | 封裝供應項目的技術資產以供客戶使用。                        |
| 潛在客戶開發註冊 | \< 1 小時  | 設定和部署潛在客戶系統。                                      |
| 發行者簽核            | \-         | 供應項目上線前發行者最終檢閱與確認。 您現在也將會有預覽供應項目的連結。 一旦您滿意預覽的外觀，按一下 [狀態] 索引標籤中的 [上線] 按鈕。此動作會傳送要求給上架小組，以將您的應用程式列在 AppSource 上。    |
| 即時                         | \< 3 小時 | 您的應用程式現在已公開列於 (「上線」) AppSource ，客戶將可以檢視您的應用程式，並在其 Power BI 訂用帳戶中部署。 您也會收到確認電子郵件。 在任何時間點，您都可以按一下 [所有供應項目] 索引標籤，查看右欄列出的所有供應項目狀態。 您可以按一下 [狀態] 索引標籤，以查看供應項目的詳細發佈流程狀態。 |
|   |   |

這個流程最多需 8 天才能完成。 完成這些發佈步驟後，您的 Power BI 應用程式供應項目會列在 [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) Power BI 應用程式區段中。


### <a name="app-certification-process"></a>應用程式認證流程

Microsoft 上架小組會使用下列流程來驗證您的 Power BI 供應項目提交：

1. 檢閱法律聲明文件和說明連結。
2. 驗證支援連絡人資訊。
3. 使用安裝程式 URL 確認可正確安裝。 
4. 掃描應用程式是否有惡意程式碼和其他惡意內容。 
5. 驗證顯示的內容是否符合應用程式的描述。
6. 應用程式的相關作業在 Power BI 中是否如預期般運作：使用範例資料開啟報表和儀表板、連線到自訂資料來源、重新整理等等。

如果認證小組發現任何問題，他們會提供意見反應。  如需 Power BI 應用程式需求的詳細資訊，請參閱 [Power BI 應用程式文件](https://go.microsoft.com/fwlink/?linkid=2028636)。


## <a name="next-steps"></a>後續步驟

我們建議您定期監視您在 [AppSource Marketplace](https://appsource.microsoft.com) 中的應用程式。  此外，您應該使用 [入口網站](https://cloudpartner.azure.com/#insights)的 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 功能，來提供您對 Marketplace 客戶及使用方式的見解。  您也可以[對您的供應項目執行特定更新](./cpp-update-existing-offer.md)。
