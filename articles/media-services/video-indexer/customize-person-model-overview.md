---
title: 在影片索引器中自訂人員模型 - Azure
titlesuffix: Azure Media Services
description: 本文概要說明影片索引器中的人員模型是什麼，以及如何加以自訂。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553720"
---
# <a name="customize-a-person-model-in-video-indexer"></a>在影片索引器中自訂人員模型

影片索引器支援您的影片中的名人辨識。 名人辨識功能能根據常被要求的資料來源 (例如 IMDB、維基百科以及 LinkedIn 最有影響力的人)，涵蓋大約 1 百萬個人臉。 無法辨識的影片索引器的表面仍偵測到，但會保留未命名。 客戶可以建置自訂的人員模型，並啟用影片索引器可辨識無法辨識的預設值的人臉。 客戶可以建置這些人員模型映像檔，此人臉部的配對的人員名稱。  

如果您的帳戶所提供不同的使用案例中，您可以從能夠建立每個帳戶的多個人員模型中獲益。 比方說，如果您的帳戶中的內容就是要排序到不同的通道，您可能想要建立個別的人員模型，每個通道。 

> [!NOTE]
> 每個人員模型支援多達 1 百萬人和每個帳戶的上限為 50 的人員模型。 

一旦建立模型之後，您就可以在針對視訊進行上傳/編製索引或重新編製索引時，藉由提供特定人員模型的模型識別碼來使用該模型。 訓練影片的新面貌，更新與視訊的特定自訂模型。 

如果您不需要多個人員模型支援，在上傳/編製索引或重新編製索引時，請勿將人員模型識別碼指派給您的視訊。 在此情況下，影片索引器會使用預設人員模型，在您的帳戶。 

您可以使用影片索引子網站編輯影片中偵測到的臉部，以及管理您的帳戶中的多個自訂人員模型中所述[來自訂人員模型使用網站](customize-person-model-with-website.md)主題。 您也可以使用 API，如中所述 [來自訂人員模型使用 Api](customize-person-model-with-api.md)。