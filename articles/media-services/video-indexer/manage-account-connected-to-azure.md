---
title: 管理影片索引器帳戶
titlesuffix: Azure Media Services
description: 本文將說明如何管理連線到 Azure 的影片索引器帳戶。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 43e08298618f059ff706d11ce6bbb3d486642872
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291814"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>管理連線到 Azure 的影片索引器帳戶

本文將示範如何管理連線到您 Azure 訂用帳戶和 Azure 媒體服務帳戶的影片索引器帳戶。

> [!NOTE]
> 您必須是影片索引器帳戶的擁有者，才能進行本主題中討論的帳戶設定調整。

## <a name="prerequisites"></a>必要條件

將您的影片索引器帳戶連線到 Azure，如[連線到 Azure](connect-to-azure.md) 中所述。 

請務必遵循文章中的[必要條件](connect-to-azure.md#prerequisites)並檢閱[考量](connect-to-azure.md#considerations)。

## <a name="examine-account-settings"></a>檢查帳戶設定

本節將探討您影片索引器帳戶的設定。

若要檢視設定：

1. 按一下右上角的使用者圖示，然後選取 [設定]。

    ![設定](./media/manage-account-connected-to-azure/select-settings.png)

2. 在 [設定] 頁面上，選取 [帳戶] 索引標籤。

如果您的影片索引器帳戶已連線到 Azure，您會看到下列內容：

* 基礎 Azure 媒體服務帳戶的名稱。
* 執行中和排入佇列的編製索引作業數目。
* 已配置的保留單元類型與數量。

如果您的帳戶需要做一些調整，您會在 [設定] 頁面上看到與帳戶設定相關的錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 如需詳細資訊，請參閱之後的[錯誤和警告](#errors-and-warnings)一節。

## <a name="auto-scale-reserved-units"></a>自動調整保留單元

[設定] 頁面可讓您設定媒體保留單元 (RU) 的自動調整。 如果選項為 [開啟]，您可以配置 RU 的數目上限，並確定該影片索引器可自動停止/啟動 RU。 使用此選項時，您不需要為閒置時間支付額外費用，也不會在索引編製負載很高時等候很長的索引編製時間。

自動調整不會低於 1 RU 或超過媒體服務帳戶的預設限制。 若要增加限制，請建立服務要求。 如需配額和限制以及如何開啟支援票證的相關資訊，請參閱 [配額和限制](../../media-services/previous/media-services-quotas-and-limitations.md)。

![註冊](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>錯誤和警告

如果您的帳戶需要做一些調整，您會在 [設定] 頁面上看到與帳戶設定相關的錯誤和警告。 這些訊息包含 Azure 入口網站中確切位置的連結，也就是您必須進行變更的位置。 本節提供有關錯誤和警告訊息的詳細資料。

* Event Grid

    您必須使用 Azure 入口網站註冊 EventGrid 資源提供者。 在 [Azure 入口網站](https://portal.azure.com/) 中，前往 [訂用帳戶] > [訂用帳戶] > [資源提供者] > [Microsoft.EventGrid]。 如果不是處於「已註冊」狀態，請按一下 [註冊]。 這需要幾分鐘來完成註冊。 

* 串流端點

    請確定基礎媒體服務帳戶有處於已啟動狀態的預設**串流端點**。 否則，您將無法從此媒體服務帳戶或影片索引器中觀看影片。

* 媒體保留單元 

    您必須在媒體服務資源上配置媒體保留單元，才能編製影片索引。 為了達到最佳的索引編製效能，建議您至少配置 10 個 S3 保留單元。 如需定價資訊，請參閱[媒體服務定價](https://azure.microsoft.com/pricing/details/media-services/)頁面的＜常見問題集＞一節。   

## <a name="next-steps"></a>後續步驟

若要以程式設計方式與連線到 Azure 的試用帳戶和/或影片索引器帳戶互動，請遵循[使用 API](video-indexer-use-apis.md) 中的指示。

您應使用連線至 Azure 時使用的相同 Azure AD 使用者。
