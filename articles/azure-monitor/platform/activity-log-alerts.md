---
title: Azure 監視器中的活動記錄警示
description: 活動記錄中發生特定事件時，透過 SMS、Webhook 及電子郵件等等收到通知。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 5d0819f71405b1bf1d4bef57a8b93d57bc879087
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244976"
---
# <a name="alerts-on-activity-log"></a>活動記錄警示 

## <a name="overview"></a>概觀
活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。 它們是 Azure 資源，因此可以使用 Azure Resource Manager 範本來加以建立。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 本文介紹活動記錄警示背後的概念。 然後將說明如何使用 Azure 入口網站來設定活動記錄事件的警示。 如需有關使用方式的詳細資訊，請參閱[建立和管理活動記錄警示](alerts-activity-log.md)。

> [!NOTE]
> 警示**無法**在活動記錄檔的警示類別目錄中建立的事件。

通常，您在下列情況要建立活動記錄警示來接收通知：

* Azure 訂用帳戶中的資源發生特定操作時，通常會將範圍限定在特定資源群組或資源。 例如，在 myProductionResourceGroup 中的任何虛擬機器被刪除時，您可能需要收到通知。 或者，您需要在將任何新角色指派給訂用帳戶中的使用者時收到通知。
* 隨即發生服務健康情況事件。 服務健康狀態事件包括適用於訂用帳戶中資源的事件 (incident) 和維護事件 (event) 通知。

簡單的比喻可以在其上的警示規則上建立活動記錄檔，了解狀況，就是瀏覽或篩選事件，透過[Azure 入口網站中的活動記錄](activity-log-view.md#azure-portal)。 在 [Azure 監視-活動記錄檔，可以篩選或尋找必要的事件並使用，以建立警示**新增活動記錄警示**] 按鈕。

在任一情況下，活動記錄警示只會監視建立警示所在之訂用帳戶中的事件。

您可以針對活動記錄事件，以 JSON 物件中任何最上層屬性作為基礎設定活動記錄警示。 如需詳細資訊，請參閱 [Azure 活動記錄概觀](./activity-logs-overview.md#categories-in-the-activity-log)。 若要深入了解服務健康情況事件，請參閱[在服務通知上接收活動記錄警示](./alerts-activity-log-service-notifications.md)。 

活動記錄警示有幾個常見的選項：

- **類別**：系統管理、服務健康狀態、自動調整、安全性、原則及建議。 
- **範圍**：個別資源或一組資源，已針對它們定義活動記錄上的警示。 活動記錄警示的範圍可在各種層級上定義：
    - 資源層級：例如，針對特定虛擬機器
    - 資源群組層級：例如，特定資源群組中的所有虛擬機器
    - 訂用帳戶層級：例如，一個訂用帳戶中的所有虛擬機器 (或) 一個訂用帳戶中的所有資源
- **資源群組**：根據預設，警示規則會儲存在與「範圍」中定義之目標的資源群組相同的資源群組中。 使用者也可以定義應該儲存警示規則的資源群組。
- **資源類型**：Resource Manager 為警示目標定義的命名空間。

- **作業名稱**：Resource Manager 角色型存取控制作業名稱。
- **層級**：事件的安全性層級 (詳細資訊、資訊、警告、錯誤或重大)。
- **狀態**：事件的狀態，通常為「已開始」、「失敗」或「成功」。
- **事件起始者**：也稱為「呼叫端」。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

> [!NOTE]
> 在訂用帳戶中，最多可為活動建立 100 個警示規則，範圍層級如下：單一資源、資源群組中所有資源或整個訂用帳戶。

當活動記錄警示啟動時，會使用動作群組來產生動作或通知。 動作群組是一組可重複使用的通知接收者，例如電子郵件地址、Webhook URL 或 SMS 電話號碼。 可以從多個警示參考接收者，將通知通道集中管理並群組。 當您定義活動記錄警示時，會有兩個選項。 您可以：

* 在您的活動記錄警示中使用現有的動作群組。
* 建立新的動作群組。

若要深入了解動作群組，請參閱[在 Azure 入口網站中建立和管理動作群組](action-groups.md)。


## <a name="next-steps"></a>後續步驟
- 取得[警示概觀](alerts-overview.md)。
- 深入了解[建立和修改活動記錄警示](alerts-activity-log.md)。
- 檢閱[活動記錄警示 Webhook 結構描述](activity-log-alerts-webhook.md)。
- 深入了解[服務健康狀態通知](service-notifications.md)。


