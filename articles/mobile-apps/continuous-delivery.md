---
title: 使用 Visual Studio App Center 和 Azure 服務，將行動應用程式的部署和發行自動化
description: 深入瞭解可協助為您的行動應用程式設定持續傳遞管線的服務（例如 App Center）。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795597"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>使用持續傳遞服務自動部署和發行您的行動應用程式

身為開發人員，您可以撰寫程式碼並將它簽入程式碼存放庫，但簽入儲存機制的認可哥能不一定一致。 當有多位開發人員處理相同的專案時，就會產生整合的問題，而小組會遇到無法運作的情況、bug 保持堆積，而且專案開發會延遲。 開發人員必須等到整個軟體程式碼建立並經過測試後，才能檢查是否有錯誤，這使得程式變慢且較不反復。

透過**持續傳遞**，您可以將行動應用程式的部署和發行自動化，不論您是將應用程式散發給一組測試人員或公司員工（適用于 Beta 測試），還是 App Store （適用于生產環境）。 它讓部署變得較不具風險，鼓勵快速反復執行，並可讓您以持續的方式將新的變更發行至您的客戶。

## <a name="distribute-application-binaries-to-beta-testers"></a>將應用程式二進位檔散發給 Beta 測試人員
搶鮮版（Beta）測試您的行動應用程式是應用程式開發過程中的其中一個重要步驟。 它有助於及早找出應用程式中的 bug 和問題，以及改善應用程式品質的意見反應，讓它準備好供生產環境使用。

使用下列服務，在您的行動應用程式中啟用持續傳遞管線。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 散發](/appcenter/distribution/)是一種工具，可讓開發人員快速將組建發行至終端使用者裝置。 透過完整的安裝入口網站體驗，散發不僅是一個功能強大的解決方案，可用於 Beta 應用程式測試者散發，同時也是在公用應用程式存放區散發的便利替代方案。 開發人員可以使用 App Center 組建和公用應用程式存放區整合，更進一步地自動化其散發工作流程。

**主要功能**
- 將**您的應用程式散發給搶鮮版（Beta）測試人員和使用者**，確保所有測試人員都是在最新版的應用程式上。
- 向**測試人員通知新版本**，而不讓測試人員再次執行下載流程。
- 管理不同應用程式版本的**通訊群組**。
- **散發至存放區** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **平臺支援**-IOS、Android、MacOS、TvOS、Xamarin、React Native、Unity、Cordova。
- 自動將 iOS 裝置註冊到您的布建設定檔。

**參考**
- [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 散發](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是功能完整的持續整合（CI）和持續傳遞（CD）服務，可與您慣用的 Git 提供者搭配運作，而且可以部署到最主要的雲端服務，包括 Azure 服務。 您可以從 GitHub、GitHub Enterprise Server、GitLab、Bitbucket Cloud 或 Azure Repos 上的程式碼開始。 然後您可以將程式碼的建置、測試和部署到 Microsoft Azure、Google Cloud Platform，或 Amazon Web Services 等等所有工作自動化。

**主要功能**
- 針對**原生（Android、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React Native）行動應用程式**設定 CI 伺服器的簡化工作體驗。
- **任何語言、平臺和雲端**-組建、測試及部署 node.js、Python、JAVA、PHP、Ruby、Go、C/C++、 C#、Android 和 iOS 應用程式。 在 Linux、macOS 和 Windows 上同時執行。 部署至雲端提供者，例如 Azure、AWS 和 GCP。 透過 Beta 頻道和應用程式商店散發行動應用程式。
- **原生容器支援**-輕鬆建立新的容器，並將其推送至任何登錄。 將容器部署至獨立主機或 Kubernetes。
- **先進的工作流程和功能**-輕鬆的組建連結和多階段組建。 支援 YAML、測試整合、發行閘道、報告等等。
- **可擴充**-使用由社區建立的一系列組建、測試和部署工作–數百個從時差到 SonarCloud 的延伸模組。 您甚至可以從其他 CI 系統（例如 Jenkins）進行部署。 Webhook 和 REST Api 可協助您整合
- 適用于公用和私人存放庫的**免費雲端託管組建**。
- **支援部署至其他雲端廠商**，例如 AWS、GCP 等。

**參考**
- [Azure Pipelines 指南入門](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>將您的應用程式直接散發至 App store
一旦您的應用程式已準備好供生產環境使用，而且您想要公開使用它，就必須將它提交給可供客戶下載的 App store。 有多種方式可將您的應用程式直接散發至應用程式存放區。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 散發](/appcenter/distribution/stores/)服務可讓您將行動應用程式直接發佈至應用程式商店。 當您的應用程式準備好供使用者下載之後，您就可以直接從 App Center 入口網站發佈您的應用程式二進位檔。  

您可以直接散發至：
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play 商店](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Apple 所開發和維護的 App Store 可讓使用者流覽及下載針對 iOS、MacOS、WatchOS 和 tvOS 裝置所開發的應用程式。 開發人員必須將其 iOS 應用程式提交至 Apple Store 以供公開使用。

### <a name="google-play"></a>Google Play

Google Play 是 Android OS 的官方 app store，可讓使用者流覽及下載針對 Android 裝置開發並透過 Google 發佈的應用程式。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management)是企業行動管理（EMM）空間中的雲端式服務，可協助讓您的員工提高生產力，同時保護公司資料。 使用 Intune，您可以 
- 管理您的員工用來存取公司資料的行動裝置和電腦。
- 管理您的員工使用的行動應用程式。
- 藉由控制您的員工存取及共用公司資訊的方式來保護您的公司資訊。
- 確保裝置和應用程式符合公司的安全性需求。
    
## <a name="deploy-updates-directly-to-users-devices"></a>將更新直接部署到使用者的裝置

### <a name="codepush"></a>CodePush
App Center 中的[CodePush](/appcenter/distribution/codepush/)服務可讓 Apache Cordova 和 React Native 開發人員直接將行動應用程式更新部署到使用者的裝置。 其運作方式為開發人員可以發佈特定更新的中央存放庫（例如，JS、HTML、CSS 和影像變更）。 然後，應用程式可以使用提供的用戶端 Sdk 來查詢存放庫中的更新。 這可讓您為使用者提供更具決定性且直接的互動模型，同時解決 bug 或新增小型功能，而不需要重新建立二進位檔或透過任何公用應用程式存放區重新散發。

**主要功能**
- 可讓 Cordova 和 React Native 開發人員直接將行動應用程式更新部署到使用者的裝置，而不需在商店上發行。
- 適用于修正 bug，或新增/移除不需要透過個別存放區重建二進位和重新發佈的小型功能。

**參考**
- [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 中的程式碼推送](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)