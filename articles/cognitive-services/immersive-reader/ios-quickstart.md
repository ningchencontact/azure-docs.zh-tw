---
title: 快速入門：建立可啟動沈浸式閱讀程式的 iOS 應用程式 (Swift)
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將從頭開始建置 iOS 應用程式，並且新增沈浸式閱讀程式功能。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899538"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>快速入門：建立可啟動沈浸式閱讀程式的 iOS 應用程式 (Swift)

[沈浸式閱讀程式](https://www.onenote.com/learningtools)是經過全面設計的工具，可實作經實證的技術以改善閱讀理解程度。

在本快速入門中，您將從頭開始建置 iOS 應用程式，並且使用沈浸式閱讀程式 SDK 來整合沈浸式閱讀程式。 本快速入門的完整工作範例可以在[這裡](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift)取得。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* 為 Azure Active Directory (Azure AD) 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./azure-active-directory-authentication.md)來設定。 設定範例專案屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。

## <a name="create-an-xcode-project"></a>建立 Xcode 專案

在 Xcode 中建立新的專案。

![New Project](./media/ios/xcode-create-project.png)

選擇 [單一檢視應用程式]  。

![新的單一檢視應用程式](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>取得 SDK CocoaPod
沈浸式閱讀程式 SDK 最簡單的使用方式是透過 CocoaPods。 若要透過 Cocoapods 進行安裝：
1. [安裝 CocoaPods](http://guides.cocoapods.org/using/getting-started.html) - 依照快速入門手冊安裝 CocoaPods。
2. 在 Xcode 專案的根目錄中執行 `pod init`，以建立 Podfile。
3.  藉由新增 `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` 將 CocoaPod 新增至 Podfile。 您的 Podfile 應該會顯示如下，並以您目標的名稱取代 quickstart-swift：
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. 在終端機中，於 Xcode 專案的目錄中執行命令 `pod install`，以安裝沈浸式閱讀程式 SDK Pod。
5. 將 `import immersive_reader_sdk` 新增至所有需要參考 SDK 的檔案。
6. 確實以開啟 `.xcworkspace` 檔案 (而非 `.xcodeproj` 檔案) 的方式開啟專案。

## <a name="acquire-an-azure-ad-authentication-token"></a>取得 Azure AD 驗證權杖

這一部分，您需要上述 Azure AD 驗證組態先決步驟中的一些值。 請回頭參考您在該工作階段儲存的文字檔。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

在包含 ViewController.swift 檔案的主要專案資料夾中，建立名為 Constants.swift 的 Swift 類別檔案。 在適用的情況下加入您的值，將類別取代為下列程式碼。 請將此檔案保存為僅存在於您電腦上的本機檔案，且切勿將此檔案認可到原始檔控制，因為其中包含不應公開的機密資料。 建議您不要將秘密保存在應用程式中。 我們建議使用後端服務來取得權杖，如此，秘密將可保存在應用程式和裝置以外。 後端 API 端點應在某種形式的驗證 (例如 [OAuth](https://oauth.net/2/)) 後方受到保護，以防止未經授權的使用者取得權杖，而使用您的沈浸式閱讀程式服務和計費；該工作不在本快速入門的討論範圍內。

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>將應用程式設定為在沒有分鏡腳本的情況下執行

開啟 AppDelegate.swift，並將檔案取代為下列程式碼。

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>建立「啟動視圖控制器」，並新增範例內容

將 ViewController.swift 重新命名為 LaunchViewController.swift，並將檔案取代為下列程式碼。

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>建置並執行應用程式

藉由選取模擬器或裝置目標，在 Xcode 中設定封存配置。
![封存配置](./media/ios/xcode-archive-scheme.png)<br/>
![選取目標](./media/ios/xcode-select-target.png)

在 Xcode 中，按 Ctrl + R 或按一下 [執行] 按鈕以執行專案，應用程式應該會在指定的模擬器或裝置上啟動。

在您的應用程式中，您應該會看到：

![範例應用程式](./media/ios/sample-app-ipad.png)

當您按一下 [沈浸式閱讀程式] 按鈕時，您會看到沈浸式閱讀程式啟動，並顯示應用程式上的內容。

![沈浸式閱讀程式](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) 和[沈浸式閱讀程式 iOS SDK 參考](./ios-reference.md)
