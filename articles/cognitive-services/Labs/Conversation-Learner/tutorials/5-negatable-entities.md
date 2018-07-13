---
title: 如何搭配對話學習模組應用程式使用 Negatable 實體 - Microsoft 認知服務 | Microsoft Docs
titleSuffix: Azure
description: 學習如何搭配對話學習模組應用程式使用 Negatable 實體。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369631"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>如何搭配對話學習模組應用程式使用 Negatable 實體

本教學課程示範實體的「Negatable」屬性。

## <a name="requirements"></a>需求
本教學課程需要執行一般教學課程聊天機器人

    npm run tutorial-general

## <a name="details"></a>詳細資料
如果使用者可以「清除」實體值，例如「不，我不要 $entity」或「不，不要 $entity」，請將動作標示為「Negatable」。 舉例來說，「不，我不要離開波士頓。」

具體而言，若有設定實體的「Negatable」屬性：

- 提及標記實體時，可讓您標示實體的正常 (正面) 執行個體和實體的「反面」執行個體。
- LUIS 會學習兩個實體模型：一個用於正面執行個體，另一個用於反面執行個體
- 實體反面執行個體的用處是清除實體變數的值 (若有)

## <a name="steps"></a>步驟

### <a name="create-the-application"></a>建立應用程式

1. 在 Web UI 中，按一下 [新增應用程式]
2. 在 [名稱] 中，輸入 NegatableEntity。 然後按一下 [建立]。

### <a name="create-an-entity"></a>建立實體

1. 按一下 [實體]，然後按一下 [新增實體]。
2. 在 [實體名稱] 中，輸入名稱。
3. 勾選 [Negatable]。
    - 這表示使用者可以提供實體的值，或表示某個值*不*是實體的值。 在後者的情況下，會將相符的實體值刪除。
3. 按一下 [建立]。

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>建立兩個動作

1. 按一下 [動作]，然後按一下 [新增動作]
2. 在 [回應] 中，鍵入「I don't know your name」(我不知道您的姓名)。
3. 在 [Disqualifying Entities]\(不合格的實體\) 中，輸入姓名。
3. Click Create

然後建立第二個動作。

1. 按一下 [動作]，然後按一下 [新增動作] 建立第二個動作。
3. 在 [回應] 中，鍵入「I know your name. It is $name」(我知道您的姓名。您叫 $name)。
4. Click Create

現在您有兩個動作。

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>訓練聊天機器人

1. 按一下 [Train Dialogs]\(訓練對話\)，然後按一下 [New Train Dialog]\(新增訓練對話\)。
2. 鍵入「hello」(您好)。
3. 按一下 [Score Actions]\(評分動作\)，並選取 [I don't know your name]\(我不知道您的姓名\)。
    - 請注意，這會得到 100% 分數，因為這是唯一的有效動作。
2. 輸入「my name is david」(我的名字叫 david)
3. 選取 [david]，並選擇標籤 [+name]|\(+姓名\)
    - 請注意，「姓名」有兩個執行個體：「+姓名」和「-姓名」。  加號表示會提供該值。 減號表示告訴系統某個值並非值。
5. 按一下 [Score Actions]\(評分動作\)
    - 請注意，名稱值現在是在聊天機器人記憶體中。
    - 「I know your name. It is $name」(我知道您的姓名。您叫 $name) 是唯一可用的回應。 
6. 選取 I know your name. It is $name」(我知道您的姓名。您叫 $name)。

讓我們嘗試清除 Negatable 實體：

7. 輸入「my name is not david」\(我的名字不叫 david\)。
    - 請注意，「不」是由於先前的模式而選取做為姓名。 這不正確。
2. 按一下 [不]，然後按一下紅色 x。 
3. 按一下 [david]。
    - 這現在是負面實體，表示這不是姓名實體的值。
2. 選取 [-name]\(-姓名\)。
3. 按一下 [Score Actions]\(評分動作\)。
    - 可發現已從記憶體清除該值。
2. 選取 [I don't know your name]\(我不知道您的姓名\)，這是唯一的動作。

以下將示範如何輸入姓名的新值。

3. 輸入「john」做為姓名。 然後選取 [john]，並按一下 [姓名]。
4. 按一下 [Score Actions]\(評分動作\)。
5. 選取 I know your name. It is $name」(我知道您的姓名。您叫 $name)。

現在嘗試取代輸入的姓名。

6. 輸入「my name is susan」(我的名字叫 susan)。
7. 選取 I know your name. It is $name」(我知道您的姓名。您叫 $name)。
7. 按一下 [Score Actions]\(評分動作\)。
8. 請注意，**susan** 已覆寫實體值中的 **john**。
9. 輸入「my name is not susan」(我的名字不叫 susan)。
    - 請注意，系統已將此標示為負面執行個體。
2. 按一下 [Score Actions]\(評分動作\)。
3. 選取 [I don't know your name]\(我不知道您的姓名\)，這是唯一的動作。
7. 按一下 [Done Teaching]\(完成教學\)。

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [多重值實體](./6-multi-value-entities.md)
