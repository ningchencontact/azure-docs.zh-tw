---
title: 依國家/地區限制 Azure CDN 內容 | Microsoft Docs
description: 了解如何使用地區篩選功能限制您的 Azure CDN 內容存取。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 93321c4c8a7f8d79835d702ca07132eed94f6493
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260747"
---
# <a name="restrict-azure-cdn-content-by-country"></a>依國家/地區限制 Azure CDN 內容

## <a name="overview"></a>概觀
當使用者要求您的內容時，預設會提供內容，不論使用者從哪裡提出這項要求。 在某些情況下，您可能想要依國家 (地區) 限制存取您的內容。 本文說明如何使用「地區篩選」功能設定服務，以依國家 (地區) 允許或封鎖存取。

> [!IMPORTANT]
> Azure CDN 產品全都提供相同地理篩選功能，不過兩者支援的國家 (地區) 代碼之間有些許差異。 如需詳細資訊，請參閱 [Azure CDN 國家/地區代碼](https://msdn.microsoft.com/library/mt761717.aspx)。


如需適用於設定此類限制之考量的資訊，請參閱[考量](cdn-restrict-access-by-country.md#considerations)。  

![國家 (地區) 篩選](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>步驟 1：定義目錄路徑
> [!IMPORTANT]
> **來自 Microsoft 的標準 Azure CDN** 設定檔不支援以路徑為基礎的地區篩選。
>


選取您在入口網站內的端點，然後尋找左邊導覽上的 [地區篩選] 索引標籤，以找到這項功能。

在設定國家 (地區) 篩選器時，您必須指定要允許或拒絕使用者存取之位置的相對路徑。 您可藉由指定目錄路徑 /pictures/，使用正斜線 (/) 或選取的資料夾來為所有檔案套用地區篩選。 您也可以將地區篩選套用至單一檔案，方法是指定檔案，並保留結尾斜線 "/pictures/city.png"。

目錄路徑篩選器範例：

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>步驟 2：定義動作：封鎖或允許
**封鎖：** 將拒絕來自指定國家 (地區) 的使用者，存取從該遞迴路徑要求的資產。 若尚未設定該位置的其他國家 (地區) 篩選選項，則其他所有使用者都將允許存取。

**允許：** 只允許來自指定國家 (地區) 的使用者存取從該遞迴路徑要求的資產。

## <a name="step-3-define-the-countries"></a>步驟 3：定義國家 (地區)
選取您想要封鎖或允許路徑的國家 (地區)。 

例如，封鎖 /Photos/Strasbourg/ 的規則會篩選檔案，包括：

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>國碼
地區篩選功能可使用國碼 (地區碼) 來定義國家 (地區)，以允許或封鎖該國家 (地區) 對受保護目錄的要求。 雖然 Azure CDN 產品全都提供相同地理篩選功能，但兩者支援的國家 (地區) 代碼之間有些許差異。 如需詳細資訊，請參閱 [Azure CDN 國家/地區代碼](https://msdn.microsoft.com/library/mt761717.aspx)。 

## <a name="considerations"></a>考量
* 您對國家/地區篩選組態的變更不會立即生效：
   * 若為**來自 Microsoft 的標準 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
   * 若為**來自 Akamai 的標準 Azure CDN** 設定檔，通常會在一分鐘內完成傳播。 
   * 若為**來自 Verizon 的標準 Azure CDN** 和**來自 Verizon 的進階 Azure CDN** 設定檔，通常會在 10 分鐘內完成傳播。 
 
* 這項功能不支援萬用字元 (例如，‘*’)。

* 會將相對路徑相關聯的地區篩選組態，遞迴地套用到該路徑。

* 只能對相同的相對路徑套用一個規則。 也就是說，您無法建立多個指向相同相對路徑的國家 (地區) 篩選。 然而，由於國家 (地區) 篩選的遞迴本質，資料夾可以有多個國家 (地區) 篩選。 換句話說，可以將不同的國家 (地區) 篩選指派給先前設定之資料夾的子資料夾。

