---
title: 選擇使用 Visual Studio 和 Azure 服務來建立用戶端應用程式的前端開發平臺
description: 瞭解支援的原生和跨平臺語言，以建立用戶端應用程式。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795493"
---
# <a name="choose-mobile-development-framework"></a>選擇 mobile 開發架構
開發人員可以使用用戶端技術來建立行動應用程式本身，並使用特定的架構和模式來進行跨平臺的方法。 開發人員可以根據其決策，建立原生（使用像是目標 C 和 JAVA 的語言）應用程式、使用 Xamarin、.NET C#和的跨平臺應用程式，以及混合式（使用 Cordova）及其變體因素.

## <a name="native-platforms"></a>原生平臺
建立原生應用程式需要平臺特定的程式設計語言、Sdk、開發環境，以及 OS 廠商所提供的其他工具。

### <a name="ios"></a>iOS
由 Apple 建立和開發的應用程式建置於 iOS 上，也就是在 iPhone 和 iPad 上執行。

- 程式**設計語言**-目標-C、Swift
- **IDE** -Xcode
- **SDK** -iOS SDK

### <a name="android"></a>Android
建置於 Android 上的應用程式是由 Google 和世界上最熱門的 OS 所設計，可在各種智慧型手機和平板電腦上執行。

- 程式**設計語言**-JAVA、Kotlin 
- **IDE** -Android Studio 和 Android 開發人員工具 
- **SDK** -Android SDK

### <a name="windows"></a>Windows
- 程式**設計語言**-C#
- **IDE** -Visual Studio，Visual Studio Code
- **SDK** -Windows SDK

#### <a name="pros"></a>優點
- 良好的使用者體驗
- 具有高效能且能夠與原生程式庫互動的回應式應用程式
- 高度安全的應用程式

#### <a name="cons"></a>缺點
- 應用程式只會在一個平臺上執行
- 建立應用程式的開發人員資源和成本更高
- 較低的程式碼重複使用

## <a name="cross-platforms-and-hybrid-applications"></a>跨平臺和混合式應用程式
跨平臺應用程式可讓您在 iOS、Android 和 Windows 上撰寫原生行動應用程式一次、共用程式碼，並加以執行。

### <a name="xamarin"></a>Xamarin
由 Microsoft 所擁有， [Xamarin](https://visualstudio.microsoft.com/xamarin/)可讓您在中C#建立強大的跨平臺行動應用程式，並提供可跨許多平臺（包括 IOS、Android 和 Windows）運作的類別庫和執行時間，同時仍可編譯原生（非解讀）高效能的應用程式。 Xamarin 結合了原生平臺的所有功能，並加入了許多強大的功能。

- 程式**設計語言**-C#
- **IDE** -Windows 或 Mac 上的 Visual Studio

### <a name="react-native"></a>React Native
由2015中的 Facebook 發行， [React Native](https://facebook.github.io/react-native/)是一個[開放原始](https://github.com/facebook/react-native)碼 JavaScript 架構，用於撰寫適用于 iOS 和 Android 的真實、原生轉譯行動應用程式。 它是根據回應、Facebook 的 JavaScript 程式庫來建立使用者介面，而不是以瀏覽器為目標，而是以行動平臺為目標。 React Native 會使用原生元件，而不是 web 元件做為建立區塊。
 
- 程式**設計語言**-JavaScript
- **IDE** -Visual Studio Code

### <a name="unity"></a>Unity
 Unity 是針對建立遊戲而優化的引擎，可讓開發人員C#針對不同的平臺（包括 Windows、IOS、Android 和 Xbox）製作高品質的 2D/3d 應用程式。

### <a name="cordova"></a>Cordova
Cordova 可讓您使用適用于 Apache Codova 的 Visual Studio Tools 或具有 Cordova 延伸模組的 Visual Studio Code，來建立混合式應用程式。 透過混合式方法，您可以與網站共用元件，並使用以 Cordova 為基礎的託管 web 應用程式來重複使用網頁伺服器應用程式。

#### <a name="pros"></a>優點
- 為多個平臺建立一個程式碼基底，以提高程式碼的可用性
- 滿足多個平臺之間的更廣大物件
- 大幅減少開發時間
- 易於啟動和更新

#### <a name="cons"></a>缺點
- 效能降低
- 缺乏彈性
- 每個平臺都有一組獨特的特性和功能，可讓原生應用程式更具創意
- 增加 UI 設計階段
- 工具限制
