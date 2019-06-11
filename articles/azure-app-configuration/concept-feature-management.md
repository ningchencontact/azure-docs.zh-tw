---
title: Azure 應用程式組態功能管理 | Microsoft Docs
description: 概述如何使用 Azure 應用程式組態來依需求開啟和關閉應用程式功能。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 46f39e87e4e4cf115cbc1fceeabf0dab38fade28
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393367"
---
# <a name="feature-management-overview"></a>功能管理概觀

傳統上，如果要傳送新的應用程式功能，就需要完整重新部署應用程式本身。 為了測試功能，您可能必須部署應用程式多次，以控制要在何時顯示或要讓誰看見功能。

功能管理是現代的軟體開發實務，可讓功能發行與程式碼部署脫勾，而能夠快速地依需求變更功能可用性。 它使用一種名為「功能旗標」  (也稱為「功能切換」  、「功能開關」  等等) 的技術來動態管理功能的生命週期。

功能管理可協助開發人員處理下列問題：

* **程式碼分支管理**：功能旗標經常用來包裝正在開發的新應用程式功能。 這類功能預設會「隱藏」起來。 您可以安全地傳送功能 (即使尚未完成)，讓其在生產環境中保持隱匿狀態。 使用這個稱為「隱匿部署」  的方法，您便可以在每個開發週期結束時發行所有程式碼。 您不再需要因為分多次才完成一項功能，而必須跨多個週期來維護程式碼分支。
* **在生產環境中測試**：您可以使用功能旗標在生產環境中授與及早存取新功能的能力。 例如，您可以限制只有小組成員或內部的 Beta 版測試人員才能獲得此存取權。 這些使用者會獲得完整無缺的生產環境體驗，而不是測試環境中的模擬或部分體驗。
* **發行小眾測試版**：您也可以使用功能旗標，漸進地對使用者推出新功能。 您可以先鎖定一小部分的使用者人數來推出，等到一段時間過後，您對實作更有信心時再逐漸增加該百分比。
* **立即終止切換**：功能旗標天生就能讓您安全地發行新功能。 這些旗標可讓您隨時開啟和關閉應用程式功能。 如有必要，您可以快速停用功能，而無須重建和重新部署應用程式。
* **選擇性啟用**：雖然大部分的功能旗標只會存在到其相關聯的功能成功發行時，但有部分功能旗標卻可以長期存在。 您可以使用功能旗標來區隔使用者，並對每個群組傳遞一組特定的功能。 例如，您可以設定只會在特定網頁瀏覽器運作的功能。 您可以定義功能旗標，從而只讓該瀏覽器的使用者看見並使用該功能。 藉由此方法，您可以在之後輕鬆地擴充支援的瀏覽器清單，而不必變更程式碼。

## <a name="basic-concepts"></a>基本概念

以下是數個與功能管理有關的新詞彙：

* **功能旗標**：功能旗標是擁有二元狀態「開啟」  或「關閉」  的變數。 功能旗標也有相關聯的程式碼區塊。 功能旗標的狀態會觸發是否要執行程式碼區塊。
* **功能管理員**：功能管理員是應用程式套件，可處理應用程式中所有功能旗標的生命週期。 功能管理員通常會提供額外的功能，例如快取功能旗標和更新其狀態。
* **篩選條件**：篩選條件是用於評估功能旗標狀態的規則。 舉例來說，使用者群組、裝置或瀏覽器類型、地理位置和時間範圍，全都是篩選條件所能代表的項目。

功能管理的有效實作至少須由兩個搭配運作的元件組成：

* 一個使用功能旗標的應用程式。
* 一個儲存功能旗標及其目前狀態的個別存放庫。

下列範例說明這些元件的互動方式。

## <a name="feature-flag-usage-in-code"></a>程式碼中的功能旗標使用方式

在應用程式中實作功能旗標的基本模式很簡單。 您可以將功能旗標視為在程式碼中與 `if` 條件陳述式搭配使用的布林值狀態變數：

```csharp
if (featureFlag) {
    // Run the following code
}
```

在此情況下，如果 `featureFlag` 設定為 `True`，就會執行以括號括住的程式碼區塊；否則會略過。 您可用靜態方式設定 `featureFlag` 的值，如下列程式碼範例所示：

```csharp
bool featureFlag = true;
```

您也可以根據特定規則來評估旗標的狀態：

```csharp
bool featureFlag = isBetaUser();
```

稍微複雜一點的功能旗標模式也包括 `else` 陳述式：

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

不過，您可以在基本模式中重新撰寫此行為。 [在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)會說明以簡單程式碼模式作為標準的優點。 例如︰

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
}

if (!featureFlag) {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>功能旗標存放庫

若要有效地使用功能旗標，您需要將應用程式中使用的所有功能旗標放在外部。 如此，您就可以變更功能旗標狀態，而不必修改和重新部署應用程式本身。

Azure 應用程式組態的設計目的是要作為功能旗標的集中存放庫。 您可以使用 Azure 應用程式組態來定義不同類型的功能旗標，並放心地快速操作其狀態。 接著，您可以使用適用於各種程式設計語言架構的應用程式組態程式庫，輕鬆地從應用程式存取這些功能旗標。

[在 ASP.NET Core 應用程式中使用功能旗標](./use-feature-flags-dotnet-core.md)說明如何搭配使用 .NET Core 應用程式組態提供者和功能管理程式庫，為您的 ASP.NET Web 應用程式實作功能旗標。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將功能旗標新增至 ASP.NET Core Web 應用程式](./quickstart-feature-flag-aspnet-core.md)  
