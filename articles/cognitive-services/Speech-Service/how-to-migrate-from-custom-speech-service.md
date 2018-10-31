---
title: 從自訂語音服務移轉至語音服務
titlesuffix: Azure Cognitive Services
description: 了解如何從「自訂語音服務」移轉至「語音服務」。
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: 656a5df21d1f7dd2e7662dc3a0415581eae02b00
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471232"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>從自訂語音服務移轉至語音服務

使用本文將應用程式從「自訂語音服務」移轉至「語音服務」。

「自訂語音服務」現在已是「語音服務」的一部分。 切換至「語音服務」即可享有最新品質與功能更新的好處。
 
## <a name="migration-for-new-customers"></a>新客戶的移轉

計價模式更為簡單，轉換成適用於「語音服務」的小時型計價模式。   

1. 在提供您應用程式的每個區域中建立 Azure 資源。 Azure 資源名稱為「語音」。 您可以將單一 Azure 資源用於相同區域中的下列服務，而不建立個別的資源：

    * 語音轉文字
    * 自訂語音轉換文字
    * 文字轉換語音
    * 語音翻譯

2. 下載[語音 SDK](speech-sdk.md)。 

3. 依照快速入門指南和 SDK 範例以使用正確的 API。 如果您使用 REST API，則也必須使用正確的端點和資源索引鍵。 

4. 更新用戶端應用程式以使用「語音服務」和 API。 

> [!NOTE]
> * LUIS - 如果您已在 Language Understanding (LUIS) 中啟用語音，則相同區域中的單一 LUIS 資源不僅適用於 LUIS，也適用於所有語音服務。 請參閱[從語音辨識意圖](how-to-recognize-intents-from-speech-csharp.md)文件。
> * 文字轉換文字翻譯不是「語音服務」的一部分。 它需要自己的 Azure 資源訂用帳戶。
  


## <a name="migration-for-existing-customers"></a>現有客戶的移轉

現有客戶必須在「語音服務」入口網站上將其現有的資源索引鍵移轉至「語音服務」。 請使用下列步驟： 

> [!NOTE] 
> 資源索引鍵只能在相同的區域內進行移轉。 

1. 登入 [cris.ai](http://www.cris.ai) 入口網站，然後選取右上方功能表中的訂用帳戶。 

2. 選取 [Migrate selected subscription] \(移轉選取的訂用帳戶\)。

3. 在文字方塊中輸入訂用帳戶金鑰，然後選取 [Migrate] \(移轉\)。

## <a name="next-steps"></a>後續步驟

* [免費試用語音服務](get-started.md)
* 了解[語音轉換文字](./speech-to-text.md)概念

## <a name="see-also"></a>另請參閱

* [什麼是語音服務](overview.md)
* [語音服務和 SDK 文件](speech-sdk.md#get-the-sdk)