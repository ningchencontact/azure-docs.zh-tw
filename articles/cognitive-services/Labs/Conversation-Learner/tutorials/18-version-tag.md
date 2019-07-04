---
title: 如何使用版本標記對話學習模組模型-Azure 認知服務 |Microsoft Docs
titleSuffix: Azure
description: 了解如何搭配對話學習模組模型使用版本控制和標記。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 4067c7fb43cc200b8f49dbc14151c69a188e4e8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475718"
---
# <a name="how-to-use-version-tagging"></a>如何使用版本標記

本教學課程說明如何標記對話學習模組模型的版本，並將其中一個版本設定為「即時」。  

## <a name="requirements"></a>需求
本教學課程需要使用 Bot Framework Emulator 建立記錄對話，而非記錄對話 Web UI。  

本教學課程需要執行一般教學課程聊天機器人：

    npm run tutorial-general

## <a name="details"></a>詳細資料

標記的模型版本是靜態的；您無法加以編輯或變更。 在編輯模型時，您編輯的一律為主要版本。 當您新增標記時，對話學習模組會擷取模型在該時間點的快照集。 

您的聊天機器人將會使用您選取作為「即時」版本的模型版本，但只有在 [編輯標記] 設為 [主要] 時，才可檢視其中包含的任何對話。 如果模型的 [編輯標記] 屬性設為 [主要] 以外的任何項目，則您可以檢視模型的快照集，但無法用任何方式加以變更。

## <a name="steps"></a>步驟

### <a name="install-the-bot-framework-emulator"></a>安裝 Bot Framework Emulator

1. 移至 [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)。
2. 下載並安裝模擬器。

### <a name="create-a-model"></a>建立模型

1. 在 [模型清單首頁] 中，按一下 `New Model` 按鈕。
2. 在 `Name` 欄位中輸入 "Tutorial-18-Versioning"，並按 Enter 鍵。
4. 在左面板上按一下 [設定]。
5. 將 CONVERSATION_LEARNER_MODEL_ID 欄位的內容複製到剪貼簿。

### <a name="configure-the-emulator"></a>設定模擬器

1. 在對話學習模組根資料夾中，開啟 ".env" 檔案。
2. 在 ".env" 檔案中新增以下這一行：
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. 結束命令提示字元並重新執行，來重新啟動對話學習模組服務：
    - `npm run tutorial-general`
4. 在 Bot Framework Emulator 中建立新的聊天機器人組態，並將端點 URL 設為 `http://localhost:3978/api/messages`

### <a name="version-1"></a>第 1 版

我們將為第 1 版建立單一動作。

1. 在 Web UI 的左面板中按一下 [動作]，然後按一下 `New Action` 按鈕。
2. 在 [聊天機器人的回應] 欄位中，輸入 "hi there (version 1)" (您好 (第 1 版))。
3. 按一下 `Save` 按鈕。

現在，我們要將其標記為模型的「第 1 版」。

1. 在左面板中按一下 [設定]，然後按一下 ![](../media/tutorial18_version_tags.PNG) [版本標記] 圖示，以顯示您應點按的 `New Tag` 按鈕。
    - 將其命名為「第 1 版」
1. 在 [即時標記] 下拉式清單中，選取 [第 1 版]。  
    - 現在，使用此聊天機器人的通道將會使用模型的「第 1 版」。
    - 這個「第 1 版」模型的實體、動作和訓練對話無法再進行變更。
    - 如果您選取 [第 1 版] 作為 [編輯標記]，您將只能檢視模型，而無法加以編輯。
    - 請將 [編輯標記] 設為 [主要]，這是唯一可供編輯的模型版本。

現在，您會在 [版本標記] 方格中看到 [第 1 版]。

### <a name="version-2"></a>第 2 版

接下來我們將編輯模型，使其與第 1 版有所區別。

1. 在左面板中按一下 [動作]。
2. 在 [動作] 方格中，按一下 [您好 (第 1 版)]。
3. 將 [聊天機器人的回應] 欄位變更為 [您好 (第 2 版)]。
4. 按一下 `Save` 按鈕。
5. 按一下 `New Action` 按鈕。
6. 在 [聊天機器人的回應] 欄位中，輸入 "bye bye (version 2)" (再見 (第 2 版))。

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>確認 Bot Framework Emulator 使用的是第 1 版

1. 在 Bot Framework Emulator 中，輸入訊息 "Hey there" (您好)。
2. 請注意，聊天機器人會回應 "hi there (version 1)" (您好 (第 1 版))。
    - 如此即確認第 1 版是「即時」版本。

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>在對話學習模組 Web UI 中檢視對話記錄

1. 在左面板中按一下 [記錄對話]
    - 若未看到任何對話，請按一下重新整理按鈕。
2. 請留意方格中的「第 1 版」標記。
3. 在方格中，按一下 [您好 (第 1 版)]

> [!NOTE]
> 我們可選擇目前可用的各項對話學習模組功能來進行修正，但這些編輯的目標將是主要版本，而不是第 1 版。

您現在已了解版本控制的運作方式，以及如何使用 Bot Framework Emulator 與聊天機器人互動。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [列舉實體和集合的實體動作](./tutorial-enum-set-entity.md)
