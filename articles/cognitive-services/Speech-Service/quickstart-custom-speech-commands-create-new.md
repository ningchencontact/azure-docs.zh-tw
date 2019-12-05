---
title: 快速入門：建立自訂命令（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會建立並測試託管的自訂命令應用程式。
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815826"
---
# <a name="quickstart-create-a-custom-command-preview"></a>快速入門：建立自訂命令（預覽）

在本文中，您將瞭解如何建立和測試託管的自訂命令應用程式。
應用程式會辨識「開啟電視」之類的語句，並以簡單的訊息回應「正常，開啟電視」。

## <a name="prerequisites"></a>必要條件

- 語音訂用帳戶。 [免費試用語音服務](~/articles/cognitive-services/speech-service/get-started.md)。

  > [!NOTE]
  > 在預覽期間，訂用帳戶金鑰僅支援 westus2 區域。

- [Language Understanding](https://www.luis.ai/home) （LUIS）撰寫金鑰：
  1. 開啟網頁瀏覽器，並流覽至[Azure 入口網站](https://portal.azure.com)
  1. 選取 [建立資源]
  1. 搜尋並選取  [Language Understanding](https://aka.ms/sc-luis-all)
  1. 在建立選項中選取 [撰寫]
  1. 部署資源之後，移至資源，然後從 [快速入門] 或 [金鑰] 區段複製金鑰

      > [!div class="mx-imgBorder"]
      > ![建立撰寫資源](media/custom-speech-commands/resources-lu-authoring.png)

## <a name="go-to-the-speech-studio-for-custom-commands"></a>前往適用于自訂命令的語音 Studio

1. 開啟網頁瀏覽器，並流覽至[語音 Studio](https://speech.microsoft.com/)
1. 輸入您的認證以登入入口網站

   - 預設的視圖是您的語音訂用帳戶清單
     > [!NOTE]
     > 如果您沒有看到 [選取訂用帳戶] 頁面，您可以從頂端列的 [設定] 功能表選擇 [語音資源] 來流覽。

1. 選取您的語音訂用帳戶，然後選取 [**移至 Studio** ]
1. 選取**自訂命令（預覽）**

預設的視圖是您所建立之自訂命令應用程式的清單。

## <a name="create-a-custom-commands-project"></a>建立自訂命令專案

1. 選取 [**新增專案**] 以建立新的專案

   > [!div class="mx-imgBorder"]
   > ![建立新專案](media/custom-speech-commands/create-new-project.png)

1. 輸入 [專案名稱] 和 [語言]，然後選取 **[下一步]** 以繼續。
1. 輸入您的 LUIS 撰寫金鑰
1. 建立之後，請選取您的專案

您的 view 現在應該是您自訂命令應用程式的總覽。

## <a name="create-a-new-command"></a>建立新的命令

現在您可以建立命令。 讓我們使用會採用單一語句、`turn on the tv`，並以訊息 `Ok, turning on the TV`回應的範例。

1. 選取 [命令] 旁的 [`+`] 圖示，並為其命名，以建立新的命令 `TurnOn`
1. 選取 [儲存]。

> [!div class="mx-imgBorder"]
> ![建立命令](media/custom-speech-commands/create-add-command.png)

命令是一組：

| 群組            | 描述                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 範例句子 | 使用者可以說觸發此命令的範例語句                                                                 |
| 參數       | 完成命令所需的資訊                                                                                |
| 完成規則 | 要用來完成命令的動作。 例如，回應使用者或與另一個 web 服務通訊 |
| Advanced 規則   | 處理更具體或複雜情況的其他規則                                                              |

### <a name="add-a-sample-sentence"></a>新增範例句子

讓我們從範例句子開始，並提供使用者可以說出的範例：

```
turn on the tv
```

我們目前沒有參數，因此我們可以繼續完成規則。

### <a name="add-a-completion-rule"></a>新增完成規則

現在新增完成規則以回應使用者，指出正在採取動作。

> [!div class="mx-imgBorder"]
> ![建立完成規則](media/custom-speech-commands/create-basic-completion-response-rule.png)

| 設定    | 建議的值                        | 描述                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| 規則名稱  | "ConfirmationResponse"                 | 描述規則用途的名稱          |
| 條件 | None                                   | 判斷規則何時可執行檔條件    |
| 動作    | SpeechResponse 「確定，開啟電視」 | 規則條件為 true 時要採取的動作 |

## <a name="try-it-out"></a>歡迎試用

使用 [測試聊天] 面板來測試行為。

> [!div class="mx-imgBorder"]
> 使用 webchat](media/custom-speech-commands/create-basic-test-chat.png) ![測試

- 您輸入：「開啟電視」
- 預期的回應：「確定，開啟電視」

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
