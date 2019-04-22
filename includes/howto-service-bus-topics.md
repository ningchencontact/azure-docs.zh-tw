---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 7b05f3d8bcca5f26161f4c362078fa134518cafd
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59689060"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>什么是服务总线主题和订阅？
服務匯流排主題和訂用帳戶支援「發佈/訂閱」  訊息通訊模型。 使用主題和訂用帳戶時，分散式應用程式的元件彼此不直接通訊，相反的，他們會透過扮演中繼角色的主題來交換訊息。

![主題概念](./media/howto-service-bus-topics/sb-topics-01.png)

相較於服務匯流排佇列，其中每個訊息只由單一取用者處理，主題和訂用帳戶是採用發佈/訂閱模式，提供「一對多」的通訊形式。 一個主題可以登錄多個訂用帳戶。 當訊息傳送至主題時，每個訂用帳戶都可取得訊息來個別處理。

主題的訂用帳戶類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。 您可以每個訂用帳戶為基礎，選擇性地針對主題註冊篩選規則。 篩選規則可讓您能夠篩選或限制哪些主題訂用帳戶會接收哪些主題的訊息。

服務匯流排主題和訂用帳戶可讓您調整並處理許多使用者和應用程式上的大量訊息。

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


