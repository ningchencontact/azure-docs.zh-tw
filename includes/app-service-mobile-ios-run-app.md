---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 8146489a913ce863cee7534331231a248a3ea7ac
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890982"
---
1. 在 Mac 上，造訪 [Azure 入口網站]。 按一下 [所有服務] > [應用程式服務] > 您剛才建立的後端。 在行動應用程式設定中，選擇您慣用的語言：

    - Objective-C &ndash; **快速入門** > **iOS (Objective-C)**
    - Swift &ndash; **快速入門** > **iOS (Swift)**

    在 [3. 定義動作群組]**在 [設定用戶端應用程式]** 下方，按一下 [下載]。 這會下載已預先設定為會連線至後端的完整 Xcode 專案。 使用 Xcode 開啟專案。

1. 按 [執行]  按鈕以建置專案，並在 iOS 模擬器中啟動應用程式。

1. 在應用程式中，按一下加號 (**+**) 圖示，輸入有意義的文字 (例如 *Complete the tutorial*)，然後按一下儲存按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。

   ![在 iOS 上執行的快速入門應用程式](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure 入口網站]: https://portal.azure.com/
