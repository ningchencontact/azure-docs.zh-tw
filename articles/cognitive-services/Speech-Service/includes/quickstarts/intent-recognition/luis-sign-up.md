---
title: 快速入門：辨識語音、意圖和實體，Python - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660461"
---
若要完成意圖辨識快速入門，您必須使用 LUIS 預覽入口網站建立 LUIS 帳戶和專案。 本快速入門只需要 LUIS 訂用帳戶。 不需要語音服務訂用帳戶。

您的首要工作，是使用 LUIS 預覽入口網站建立 LUIS 帳戶和應用程式。 您所建立的 LUIS 應用程式將使用預先建置的網域進行家庭自動化，以提供意圖、實體和範例語句。 完成之後，您會擁有在雲端中執行的 LUIS 端點，供您使用語音 SDK 進行呼叫。 

請依照下列指示建立 LUIS 應用程式： 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">快速入門：建置預先建置的網域應用程式</a>

完成作業後，您將需要三項資訊： 

* 您的 LUIS 金鑰
* 您的 LUIS 區域
* 您的 LUIS 應用程式識別碼

您可以在此處透過 [LUIS 預覽入口網站](https://preview.luis.ai/)找到這些資訊：

1. 在 LUIS 預覽入口網站中選取 [管理]  ，然後選取 [Azure 資源]  。 在此頁面上，您可以找到您的 LUIS 金鑰和位置 (有時也稱為_區域_)。  

   > [!div class="mx-imgBorder"]
   > ![LUIS 金鑰和位置](../../../media/luis/luis-key-region.png)

2. 取得金鑰和位置之後，您將需要應用程式識別碼。 選取 [應用程式設定]  -- 此頁面會提供您的應用程式識別碼。

   > [!div class="mx-imgBorder"]
   > ![LUIS 應用程式識別碼](../../../media/luis/luis-app-id.png)