---
title: 如何搭配對話學習模組應用程式使用版本控制和標記 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 學習如何搭配對話學習模組應用程式使用版本控制和標記。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369615"
---
# <a name="how-to-use-versioning-and-tagging"></a>如何使用版本控制和標記

本教學課程說明如何標記對話學模組應用程式的版本，並設定其中一個版本為「即時」。  

## <a name="requirements"></a>需求
本教學課程需要使用聊天機器人模擬器建立記錄對話，而非記錄對話 Web UI。  

本教學課程需要執行一般教學課程聊天機器人：

    npm run tutorial-general

## <a name="details"></a>詳細資料

編輯時，您編輯的都是「主要」標記，您可以從主要標記建立加上標記的版本 (基本上等於是在建立主要標記的快照集)，但是您無法編輯已加上標記的版本。

## <a name="steps"></a>步驟

### <a name="install-the-bot-framework-emulator"></a>安裝 Bot Framework 模擬器

- 移至 [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator)。
- 下載並安裝模擬器。

### <a name="create-an-app"></a>建立應用程式

1. 按一下 [新增應用程式]
2. 在 [名稱] 欄位中，輸入 Tutorial-16-Versioning
3. Click Create 
4. 按一下 [設定]
5. 複製應用程式識別碼

### <a name="configure-the-emulator"></a>設定模擬器

- 在對話學習模組根資料夾中，開啟 .env 檔案。
- 貼上應用程式識別碼做為 CONVERSATION_LEARNER_APP_ID 的值
- 結束命令提示字元並重新執行，來重新啟動對話學習模組服務：
 
    npm run tutorial-general 

### <a name="actions"></a>動作

讓我們來建立動作：

1. 切換至 Web UI。
1. 按一下 [動作]，然後按一下 [新增動作]。
2. 在 [回應] 中，輸入「您好 (version 1)」。
3. 按一下 [儲存]。


![](../media/tutorial16_action1.PNG)

建立新的標記：

3. 按一下 [設定]，並建立新的「標記」。
    - 將名稱設為「第 1 版」
4. 將「第 1 版」設定為「即時」。  
    - 設定「第 1 版」為即時標記後，使用此一聊天機器人的通道將使用「第 1 版」這個標記。
    - 加上標記的應用程式版本不受編輯 (變更動作、實體、新增訓練對話) 所影響。  
    - 對於應用程式的編輯 (變更、實體、新增訓練對話) 一律針對「主要」標記進行。  換句話說，「主要」標記是唯一可以變更的標記；其他標記都是固定的快照集。
    - 對話學習模組 UI 中的記錄對話一律使用主要標記 (而非即時標記)。

![](../media/tutorial16_v1_create.PNG)

注意已在設定中建立版本：

![](../media/tutorial16_settings.PNG)

讓我們加入第二個動作：

1. 按一下 [動作]，然後按一下 [新增動作]。
2. 在 [回應] 中，輸入「bye bye (version 2)」(再見 (version 2))。

編輯第一個動作：

1. 按一下 [動作]。
2. 在 [動作] 下，按一下 [hi there (version 1)]\(您好 (version 1)\)。
3. 變更回應為「hi there (version 2)」(您好 (version 2))。

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>切換至聊天機器人模擬器

1. 在聊天機器人 UI 中，輸入「goodbye」(再見)。
2. 請注意，聊天機器人會回應「hi there (version 1)」(您好 (version 1))。
    - 這表示第 1 版為「即時」。 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>切換至 Web UI

1. 按一下 [記錄對話] (如果您並未看到任何對話，請重新整理應用程式)。
2. 按一下 [hi there (version 2)]\(您好 (version 2)\)

請注意，我們可以從所有目前可用的動作中選擇動作，進行修正。 這些編輯都是對主要標記進行。

您現在已經瞭解版本控制的運作方式，以及您可以如何使用 Bot Framework 模擬器與聊天機器人進行互動。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [示範 - 密碼重設](./demo-password-reset.md)
