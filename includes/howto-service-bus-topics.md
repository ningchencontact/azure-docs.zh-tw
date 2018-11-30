---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330810"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>什麼是服務匯流排主題和訂用帳戶？
服務匯流排主題和訂用帳戶支援「發佈/訂閱」  訊息通訊模型。 使用主題和訂用帳戶時，分散式應用程式的元件彼此不直接通訊，相反的，他們會透過扮演中繼角色的主題來交換訊息。

![主題概念](./media/howto-service-bus-topics/sb-topics-01.png)

相較於服務匯流排佇列，其中每個訊息只由單一取用者處理，主題和訂用帳戶是採用發佈/訂閱模式，提供「一對多」的通訊形式。 一個主題可以登錄多個訂用帳戶。 當訊息傳送至主題時，每個訂用帳戶都可取得訊息來個別處理。

主題的訂用帳戶類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。 您可以每個訂用帳戶為基礎，選擇性地針對主題註冊篩選規則。 篩選規則可讓您能夠篩選或限制哪些主題訂用帳戶會接收哪些主題的訊息。

服務匯流排主題和訂用帳戶可讓您調整並處理許多使用者和應用程式上的大量訊息。

## <a name="create-a-namespace"></a>建立命名空間
若要開始在 Azure 中使用服務匯流排主題和訂用帳戶，首先必須建立「服務命名空間」 。 命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。

若要建立命名空間：

1. 登入 [Azure 入口網站][Azure portal]。
2. 在入口網站的左方瀏覽窗格中，依序按一下 [建立資源]、[企業整合] 及 [服務匯流排]。
3. 在 [建立命名空間]  對話方塊中，輸入命名空間名稱。 系統會立即檢查此名稱是否可用。
4. 確定命名空間名稱可用之後，請選擇定價層 ([基本]、[標準] 或 [進階])。
5. 在 [訂用帳戶]  欄位中，選擇要在其中建立命名空間的 Azure 訂用帳戶。
6. 在 [資源群組] 欄位中，選擇要存留命名空間的現有資源群組，或是建立新的資源群組。      
7. 在 [位置] 中，選擇應裝載您命名空間的國家或地區。
   
    ![建立命名空間][create-namespace]
8. 按一下 [ **建立** ] 按鈕。 此時系統會建立並啟用命名空間。 系統為帳戶提供資源時，您可能需要等幾分鐘。

### <a name="obtain-the-credentials"></a>取得認證
1. 在命名空間清單中，按一下新建立的命名空間名稱。
2. 在 [服務匯流排命名空間] 窗格中，按一下 [共用存取原則]。
3. 在 [共用存取原則] 窗格中，按一下 [RootManageSharedAccessKey]。
   
    ![connection-info][connection-info]
4. 在 [原則: RootManageSharedAccessKey] 窗格中，按一下 [連接字串 - 主要金鑰] 旁邊的複製按鈕，將連接字串複製到剪貼簿以供稍後使用。
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


