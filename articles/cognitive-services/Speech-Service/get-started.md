---
title: 免費試用語音服務
description: 探索如何免費試用語音服務。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325202"
---
# <a name="try-the-speech-service-for-free"></a>免費試用語音服務

開始使用語音服務相當簡單且價格合理。 30 天免費試用版可讓您探索服務可以執行哪些作業，以及判斷它是否符合您的應用程式需求。

如果您需要更多時間，請登入 Microsoft Azure 帳戶，其隨附 $200 美元的服務額度，您可以將此額度應用於長達 30 天的付費語音服務訂用帳戶。

最後，語音服務會提供適用於開發應用程式的免費少量定價層。 即使在服務額度到期後，您仍可保留此免費訂用帳戶。

## <a name="free-trial"></a>免費試用

30 天免費試用可讓您在有限的時間內存取 S0 標準定價層。 若要註冊 30 天免費試用，請遵循下列步驟。

1. 前往[試用認知服務](https://azure.microsoft.com/try/cognitive-services/)頁面。

1. 切換至 [語音] 索引標籤，然後按一下「語音服務」旁邊的 [取得 API 金鑰] 按鈕。

   ![語音服務索引標籤](media/index/try-speech-api-free-trial1.png)<br>
   ![API 金鑰](media/index/try-speech-api-free-trial2.png)

3. 同意條款，並從下拉式功能表中選取您的地區設定。

   ![同意條款](media/index/try-speech-api-free-trial3.png)

4. 使用 Microsoft、Facebook、LinkedIn 或 GitHub 帳戶登入。 或者，可以註冊免費的 Microsoft 帳戶：

    * 前往 [Microsoft 帳戶入口網站](https://account.microsoft.com/account)。
    * 按一下 [以 Microsoft 登入]。

    ![登入](media/index/try-speech-api-free-trial4.png)

    * 當系統要求您登入時，請按一下 [建立一個]。

    ![建立新帳戶](media/index/try-speech-api-free-trial5.png)

    * 在後續步驟中，請輸入您的電子郵件地址或電話號碼、指派密碼，並遵循指示來確認新的 Microsoft 帳戶。

登入之後，即可開始免費試用。 顯示的網頁會列出您目前有試用訂用帳戶的所有認知服務。 「語音服務」旁邊會列出兩個訂用帳戶金鑰。 您可以在應用程式中使用任一金鑰。

> [!NOTE]
> 所有免費試用訂用帳戶都位於美國西部區域。 在提出要求時，請務必使用您區域的對應端點。

## <a name="new-azure-account"></a>新的 Azure 帳戶

新的 Azure 帳戶會收到持續長達 30 天的 $200 美元服務額度。 此額度可用來進一步探索語音服務，或開始進行應用程式開發。

若要註冊新的 Azure 帳戶，請遵循下列步驟。

1. 前往 [Azure 註冊頁面](https://azure.microsoft.com/free/ai/)。 

1. 按一下 [開始免費使用]。

    ![開始免費使用](media/index/try-speech-api-new-azure1.png)

3. 使用您的 Microsoft 帳戶登入。 如果您沒有帳戶：

    * 前往 [Microsoft 帳戶入口網站](https://account.microsoft.com/account)。
    * 按一下 [以 Microsoft 登入]。
    * 當系統要求您登入時，請按一下 [建立一個]。
    * 在後續步驟中，請輸入您的電子郵件地址或電話號碼、指派密碼，並遵循指示來確認新的 Microsoft 帳戶。

1. 輸入註冊帳戶時所要求的其餘資訊。 指定您的國家/地區和您的名稱，並提供電話號碼和電子郵件地址。

    ![輸入資訊](media/index/try-speech-api-new-azure2.png)

    經由電話並提供信用卡號碼來驗證您的身分識別，然後接受 Azure 使用者合約。 (不會向您的信用卡計費。)

    ![接受合約](media/index/try-speech-api-new-azure3.png)

已建立您的免費 Azure 帳戶。 遵循下一節中的步驟，以開始訂閱語音服務。

## <a name="create-a-speech-resource-in-azure"></a>在 Azure 中建立語音資源

若要將語音服務資源新增至您的 Azure 帳戶，請遵循下列步驟。

1. 使用您的 Microsoft 帳戶，登入 [Azure 入口網站](https://ms.portal.azure.com/)。

1. 按一下入口網站左上角的 [建立資源] (綠色 **+** 圖示)。

    ![建立資源](media/index/try-speech-api-create-speech1.png)

1. 在 [新增] 視窗中，搜尋 Speech。

    ![按一下 Speech](media/index/try-speech-api-create-speech2.png)

1. 在搜尋結果中按一下 [Speech (預覽)]。

1. 按一下語音服務面板底部的 [建立] 按鈕。

    ![Click Create](media/index/try-speech-api-create-speech3.png)

1. 在 [建立] 面板中，輸入：

    * 新資源的名稱。 此名稱可協助您區分相同服務的多個訂用帳戶。
    * 選擇與新資源相關聯的 Azure 訂用帳戶來決定費用的計費方式。
    * 選擇將使用此資源的區域。 目前，語音服務適用於東亞、北歐和美國西部區域。
    * 選擇定價層，不是 F0 (有限的免費訂用帳戶) 就是 S0 (標準訂用帳戶)。 如需每一層的定價和使用量配額完整資訊，請按一下 [檢視完整定價詳細資料]。
    * 為此語音訂用帳戶建立新的資源群組，或將它指派給現有的資源群組。 資源群組可協助組織各種 Azure 訂用帳戶。
    * 為了方便未來存取您的訂用帳戶，請核取 [釘選到儀表板] 核取方塊。
    * 按一下 [建立]。

    ![按一下面板中的建立](media/index/try-speech-api-create-speech4.png)

    建立和部署新的語音資源可能需要一些時間。 [快速入門] 面板會顯示新資源的相關資訊。

    ![快速入門面板](media/index/try-speech-api-create-speech5.png)

1. 按一下 [快速入門] 面板中步驟 1 之下的 [金鑰] 連結，以顯示您的訂用帳戶金鑰。 每個訂用帳戶都有兩個金鑰，您可以在應用程式中使用任一個。 按一下每個金鑰旁邊的按鈕，將它複製到剪貼簿，以便貼到您的程式碼中。

> [!NOTE]
> 您可以在一或多個區域中建立任意數目的標準層訂用帳戶。 不過，您可以建立只有一個免費層的訂用帳戶。

## <a name="next-steps"></a>後續步驟

完成個我們的任何一個 10 分鐘快速入門，或查看我們的 SDK 範例。

> [!div class="nextstepaction"]
> [快速入門：以 C# 辨識語音](quickstart-csharp-dotnet-windows.md)
> [語音 SDK 範例](speech-sdk.md#get-the-samples)
