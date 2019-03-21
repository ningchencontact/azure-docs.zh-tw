---
title: 使用 Azure 上的語音服務建立客製化語音端點 |Microsoft Docs
description: 了解如何建立使用 Azure 語音服務的自訂語音轉換文字端點。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 03/12/2018
ms.author: panosper
ms.openlocfilehash: 1f7a84d187ba6279caad4926d54bfc56254152af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862993"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>建立自訂的語音轉文字端點

在您建立自訂原音模型或語言模型之後，您可以將這些模型部署至自訂的語音轉文字端點。

## <a name="create-an-endpoint"></a>建立端點
若要建立新的自訂端點，請在頁面頂端的 [自訂語音] 功能表上選取 [端點]。 這個動作會帶您前往 [端點] 頁面，內含目前自訂端點的資料表。 如果您尚未建立任何端點，此資料表會是空白的。 資料表標題會反映目前的地區設定。

若要建立不同語言的部署，請選取 [變更地區設定]。 如需支援語言的詳細資訊。

若要建立新的端點，請選取 [新建]。 在 [建立端點] 窗格中，輸入自訂部署的 [名稱] 和 [描述] 方塊中所含的資訊。

在 [訂用帳戶] 下拉式方塊中，選取您要使用的訂用帳戶。 如果是 S0 (付費) 訂用帳戶，您可以選取並行要求。

您也可以選取要開啟還是關閉內容記錄。 也就是說，您可以選取是否要儲存端點流量。 如果未選取，則會隱藏儲存流量的選項。 針對所有記錄下來的內容，您可以在 [端點] -> [詳細資料] 檢視下找到下載連結

在 [原音模型] 清單中，選取所需的原音模型，然後在 [語言模型] 清單中，選取所需的語言模型。 原音和語言模型的選項一律包含基本 Microsoft 模型。 所選取的基本模型會限制組合內容。 您無法混合使用交談式基本模型與搜尋服務，也無法決定基本模型。

> [!NOTE]
> 請務必選取核取方塊來接受使用規定和定價資訊。
>

在選取了原音和語言模型之後，請選取 [建立]。 這個動作會讓您回到 [部署] 頁面。 資料表中現在包含與新的端點對應的項目。 端點的狀態會反映其建立當下的狀態。 可能需要 30 分鐘的時間才能具現化具有自訂模型的新端點。 當部署狀態為 [完成] 時，端點就可供使用。

部署準備就緒時，端點名稱就會變成連結。 選取連結會顯示 [端點資訊] 頁面，其中會顯示要與 HTTP 要求或 Microsoft 認知服務語音用戶端程式庫 (其使用 Web 通訊端) 搭配使用的自訂端點 URL。

## <a name="next-steps"></a>後續步驟

如需更多教學課程，請參閱：
- [取得您的語音服務試用訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
- [建立自訂原音模型](how-to-customize-acoustic-models.md)
- [建立自訂語言模型](how-to-customize-language-model.md)
