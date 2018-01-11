---
title: "智慧偵測-安全性偵測組件使用 Azure 的 Application Insights |Microsoft 文件"
description: "監視 Azure Application Insights 的潛在安全性問題的應用程式。"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3e4604a154c16b785db1ab903587ae4a35d93c05
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2017
---
# <a name="application-security-detection-pack-preview"></a>應用程式安全性偵測組件 （預覽）

Application Insights 自動分析您的應用程式所產生的遙測，並偵測到潛在的安全性問題。 這項功能可讓您識別潛在的安全性問題，並修正應用程式或必要的安全性措施，請處理它們。

這項功能需要任何特殊的安裝程式，除了[設定您的應用程式傳送遙測](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-usage-overview)。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>何時得到這種類型的智慧型偵測通知？
有三種類型的偵測到的安全性問題：
1. 不安全的 URL 存取： 應用程式中的 URL 透過 HTTP 和 HTTPS。 一般而言，接受 HTTPS 要求的 URL 不應該接受 HTTP 要求。 這可能表示您的應用程式的 bug 或安全性問題。
2. 不安全的表單： 表單 （或其他"POST"要求） 應用程式中使用 HTTP 而非 HTTPS。 使用 HTTP 可能危及使用者資料傳送的表單。
3. 可疑的使用者活動： 應用程式正在存取來自多個國家/地區的相同使用者大約相同的時間。 例如，相同的使用者會在相同的一小時內從西班牙和美國存取 應用程式。 在此偵測會指出潛在的惡意嘗試存取您的應用程式。

## <a name="does-my-app-definitely-have-a-security-issue"></a>我的應用程式確實有安全性問題嗎？
否，通知並不表示您的應用程式一定會有安全性問題。 偵測任何上述的案例，在許多情況下，可能有安全性問題。 不過，偵測可能會有自然商務的理由，您可以忽略。

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>如何修正 「 不安全的 URL 存取 > 偵測的問題？
1. **分級。** 通知提供存取不安全的 Url，以及大部分 URL 的使用者數目受到不安全的存取。 這可協助您將優先順序指派給此問題。
2. **範圍。** 使用者的百分比存取不安全的 Url？ 影響多少 Url 了？ 可以從通知取得這項資訊。
3. **診斷。** 偵測到提供的不安全的要求清單和清單的 Url 和受到影響，可協助您進一步診斷問題的使用者。

## <a name="how-do-i-fix-the-insecure-form-detection"></a>如何修正 [不安全的表單] 偵測的問題？
1. **分級。** 通知會提供不安全的表單數量及其中的資料可能會受到危害的使用者數目。 這可協助您將優先順序指派給此問題。
2. **範圍。** 形式陷入最大數目的不安全的傳輸，以及一段時間不安全傳輸的散發為何？ 可以從通知取得這項資訊。
3. **診斷。** 偵測到提供的不安全的表單清單並不安全的傳輸的每個表單，可協助您進一步診斷問題的數字的細目。

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>如何修正 「 可疑的使用者活動 」 偵測的問題？
1. **分級。** 通知提供展現可疑的行為的不同使用者的數目。 這可協助您將優先順序指派給此問題。
2. **範圍。** 從的國家/地區沒有可疑要求源自？ 哪些使用者已最可疑？ 可以從通知取得這項資訊。
3. **診斷。** 偵測到提供的可疑的使用者清單和國家 （地區） 為每個使用者，可協助您進一步診斷問題的清單。
