---
title: 依國家/地區限制 Azure CDN 內容 |Microsoft Docs
description: 了解如何使用地理篩選功能依國家/地區限制存取您的 Azure CDN 內容。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 083d8f66a73471548c812e27325e1ec69ad5c45c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869576"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>依國家/地區限制 Azure CDN 內容

## <a name="overview"></a>概觀
當使用者要求內容時，不論使用者是在什麼位置提出這項要求，依預設系統都會提供內容。 不過，在某些情況下，您可能想要依國家/地區限制您內容的存取。 具有*地區篩選*功能，您可以建立規則上的特定路徑上您的 CDN 端點，以允許或封鎖選取的國家/地區的內容。

> [!IMPORTANT]
> **來自 Microsoft 的標準 Azure CDN** 設定檔不支援以路徑為基礎的地區篩選。
> 

## <a name="standard-profiles"></a>標準設定檔
本節中的程序僅適用於**來自 Akamai 的 Azure CDN 標準**和**來自 Verizon 的 Azure CDN 標準**設定檔。 

若為**來自 Verizon 的 Azure CDN 進階**設定檔，則必須使用**管理**入口網站來啟動地區篩選。 如需詳細資訊，請參閱[來自 Verizon 的 Azure CDN 進階設定檔](#azure-cdn-premium-from-verizon-profiles)。

### <a name="define-the-directory-path"></a>定義目錄路徑
若要存取地區篩選功能，請在入口網站內選取 CDN 端點，然後在左側功能表的 [設定] 下選取 [地區篩選]。 

![地區篩選標準](./media/cdn-filtering/cdn-geo-filtering-standard.png)

從 [路徑] 方塊中，指定要允許或拒絕使用者存取的位置相對路徑。 

您可藉由指定目錄路徑 (例如，/pictures/)，並使用正斜線 (/) 或選取特定資料夾，來對所有檔案套用地區篩選。 您也可以對單一檔案套用地區篩選 (例如，/pictures/city.png)。 系統允許多個規則；在輸入規則後，會出現空白資料列供您輸入下一個規則。

例如，下列目錄路徑篩選條件全都有效：   
*/*                                 
/Photos/     
/Photos/Strasbourg/     
/Photos/Strasbourg/city.png

### <a name="define-the-type-of-action"></a>定義動作的類型

從 [動作] 清單中，選取 [允許] 或 [封鎖]： 

- **允許**:只有從指定的國家/地區的使用者可以從遞迴路徑要求的資產的存取。

- **區塊**:從指定的國家/地區的使用者被拒絕存取，從遞迴路徑要求的資產。 如果已不設定該位置的任何其他國家 （地區） 篩選選項，則所有其他使用者將不允許存取。

例如，用於封鎖 /Photos/Strasbourg/ 路徑的地區篩選規則會篩選出下列檔案：     
*http:\//\<端點 >.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<端點 >.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>定義國家/地區
從**國家/地區代碼**清單中，選取您想要封鎖或允許路徑的國家/地區。 

您完成選取國家/地區之後，請選取**儲存**以啟動新的地區篩選規則。 

![地區篩選規則](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>清除資源
若要刪除規則，請從 [地區篩選] 頁面上的清單中選取規則，然後選擇 [刪除]。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>來自 Verizon 的 Azure CDN 進階設定檔
在**來自 Verizon 的 Azure CDN 進階**設定檔上，用於建立地區篩選規則的使用者介面並不相同：

1. 在 Azure CDN 設定檔的頂端功能表中，選取 [管理]。

2. 在 Verizon 入口網站中，選取 [HTTP 大型]，然後選取 [國家 (地區) 篩選]。

    ![地區篩選標準](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. 選取 [新增國家 (地區) 篩選]。

    [步驟 1：] 頁面隨即出現。

4. 輸入目錄路徑、選取 [封鎖] 或 [新增]，然後選取 [下一步]。

    [步驟 2：] 頁面隨即出現。 

5. 從清單中，選取一或多個國家/地區，然後選取**完成**以啟動規則。 
    
    新的規則便會出現在 [國家 (地區)篩選] 頁面的資料表中。

    ![地區篩選規則](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>清除資源
在國家/地區篩選規則資料表中，選取刪除圖示旁邊的規則，將它刪除或修改它的 [編輯] 圖示。

## <a name="considerations"></a>考量
* 地區篩選組態的變更不會立即生效：
   * 若為**來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
   * 若為**來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
   * 若為**來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
 
* 這項功能不支援萬用字元 (例如 *)。

* 會將相對路徑相關聯的地區篩選組態，遞迴地套用到該路徑。

* 只能對相同的相對路徑套用一個規則。 也就是說，您無法建立指向相同的相對路徑的多個國家/地區篩選條件。 不過，因為國家 （地區） 篩選器是遞迴的則資料夾可以有多個國家/地區篩選條件。 換句話說，先前設定的資料夾的子資料夾也可以指派不同的國家/地區篩選。

* 地區篩選功能會使用國碼來定義國家/地區的要求是允許或封鎖對受保護目錄。 雖然 Akamai 和 Verizon 設定檔可支援大部分的相同國碼 (地區碼)，但兩者有一些差異。 如需詳細資訊，請參閱 [Azure CDN 國碼 (地區碼)](/previous-versions/azure/mt761717(v=azure.100))。 

