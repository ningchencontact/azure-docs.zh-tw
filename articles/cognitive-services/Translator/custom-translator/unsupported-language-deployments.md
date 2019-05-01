---
title: 不支援的語言部署-自訂轉譯器
titleSuffix: Azure Cognitive Services
description: 如何部署自訂轉譯器不支援的語言組。
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476527"
---
# <a name="unsupported-language-deployments"></a>不支援的語言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

使用 Microsoft Translator 中樞即將淘汰，Microsoft 將會解除部署所有透過中樞目前部署的模型。 許多使用者會有在中樞中自訂轉譯器不支援的語言組中部署的模型。  我們不想在此情況下有沒有辦法下轉譯其內容的使用者。

我們現在有可讓您透過自訂轉譯器將不受支援的模型部署的程序。  此程序可讓您繼續轉譯內容使用最新的 V3 API。  將裝載這些模型，直到您選擇解除部署組件或自訂轉譯程式中可用語言組。  這篇文章說明部署不支援的語言組的模型的程序。

## <a name="prerequisites"></a>必要條件

為了讓您的模型是部署的候選項目，必須符合下列準則：
* 包含模型的專案必須已移轉從中樞至使用移轉工具的自訂轉譯器。  移轉專案和工作區的程序找[此處](how-to-migrate.md)。
* 移轉發生時，模型必須是在已部署的狀態。  
* 模型的語言組必須在自訂轉譯器不支援的語言組。  或從英文，支援的語言，但組本身不會包含英文的語言組是不支援的語言部署的候選項目。  例如，法文，德文語言組的中樞模型會被視為不支援的語言組甚至不過法文，英文和英文到安裝德文為支援的語言組。

## <a name="process"></a>Process
一旦您從中樞適合部署移轉模型，您可以找到它們移至**設定**頁面上，您的工作區和向下捲動到頁面，您會看到結尾**不支援Translator 中樞訓練**一節。  如果您有符合上述必要條件的專案，才會出現這一節。

![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

內**不支援的轉譯器中樞訓練**選取頁面上，**訓練字** 索引標籤包含可進行部署的模型。  選取您想要部署，並提交一個要求的模型。   在 4 月 30 部署期限之前，您可以選取您想要部署的模型數目。
 
![如何從中樞移轉](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交之後，模型將不再提供**訓練的字**索引標籤，然後改為將會出現在**要求訓練** 索引標籤。您可以隨時檢視您所要求的訓練。

![如何從中樞移轉](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>後續步驟

當中樞已解除任務，而且所有模型都都已解除部署，則都會儲存您選取部署模型。  必須等到 5 月 24 提出要求的不受支援的模型部署。  我們會將部署在哪一個點都可透過 Translator V3 API 存取這些模型在 6 月 15 日。  此外，它們就可以透過 V2 API 直到年 7 月 1 日為止。  

如需重要日期取代中樞核取的詳細資訊[此處](https://www.microsoft.com/translator/business/hub/)。
收取一次部署，一般裝載的費用。  請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)如需詳細資訊。  

不像標準的自訂轉譯器模型，中樞模型才會提供在單一區域中，因此將不會套用多個區域裝載的費用。  一旦部署之後，您將能夠解除您中樞的模型部署在任何時候透過移轉的自訂轉譯器專案。

## <a name="next-steps"></a>後續步驟

- [將模型定型](how-to-train-model.md)。
- 透過 [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 開始使用您已部署的自訂翻譯模型。
