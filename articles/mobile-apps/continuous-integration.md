---
title: 使用 Visual Studio App Center 和 Azure 服務將應用程式的生命週期自動化
description: 深入瞭解可協助為您的行動應用程式設定持續組建和整合的服務（例如 App Center）。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795545"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>使用持續的組建和整合將應用程式的生命週期自動化

身為開發人員，您可以撰寫程式碼，並將它簽入程式碼存放庫，但簽入儲存機制的認可哥能不一定一致。 當有多位開發人員處理相同的專案時，就會產生整合的問題，而小組會遇到無法運作的情況、bug 保持堆積，而且專案開發會延遲。 開發人員必須等到整個軟體程式碼都已建立並經過測試，以檢查是否有錯誤，這使得程式變慢且較不反復。 

藉由**持續組建和整合**，開發人員可以簡化程式碼的組建和測試，而不只是將變更認可至原始程式碼存放庫，也會將測試和驗證放入組建環境中，讓它們一律執行針對其程式碼進行測試。 每當對存放庫進行認可時，對原始程式碼所做的所有變更都會持續建立。 在每次簽入時，CI 伺服器會驗證並執行開發人員所建立的任何測試。 如果測試未通過，則會傳回程序代碼進行進一步變更。 這可讓開發人員不會中斷所建立的組建，也不會在電腦本機執行所有測試，進而提高開發人員的生產力。 

## <a name="key-benefits"></a>主要權益
- **自動化**組建、測試和部署管線。
- 及早偵測**錯誤並修正問題**，以確保更快的發行速度。
- 更頻繁地**認可程式碼**並快速建立應用程式。
- **彈性**地快速變更程式碼，而不會發生任何問題。
- **更快上市時間**可確保只有良好的品質程式碼可讓您順利完成。
- **小型程式碼變更**，因為它允許一次整合一小段程式碼。
- **提高團隊的透明度和責任**，讓您不僅可以從您的客戶和小組取得**持續的意見**反應。

使用下列服務，在您的行動應用程式中啟用持續整合管線。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 組建](/appcenter/build/)服務可協助您使用安全的雲端基礎結構，建立您的小組所使用的原生和跨平臺應用程式。 您可以輕鬆地連接 App Center 中的存放庫，並在每次認可時開始在雲端中建立您的應用程式，而不需要擔心在本機設定組建伺服器、複雜的設定，以及在同事電腦上建立的程式碼。

隨著 App Center 服務的功能增加，您可以進一步將工作流程自動化。 將組建自動發行至使用 App Center 散發的測試人員和公用應用程式存放區，或在雲端中使用 App Center 測試，在數千部實際裝置和 OS 設定上執行自動化的 UI 測試。

**主要功能**
- 在短短幾分鐘內**設定持續整合**，並更頻繁且更快速地建立應用程式。
- 與**GitHub、BitBucket、Azure DevOps 和 GitLab**整合。
- 在受管理的雲端託管機器上**快速且安全的組建**。
- **讓您的組建能夠「啟動測試**」，並確認應用程式是否在實際的 IOS 和 Android 裝置上建立。
- **原生和跨平臺支援**-IOS、Android、MacOS、Windows、Xamarin React Native。
- 藉由新增複製後、預先組建和後置組建腳本**來自訂您的組建**。

**參考**
- [使用 App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [開始使用 App Center 組建](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)（Azure DevOps 中的服務）是功能完整的持續整合（CI）和持續傳遞（CD）服務，可與您慣用的 Git 提供者搭配運作，而且可以部署到大部分主要的雲端服務，包括 Azure。 您可以從 GitHub、GitHub Enterprise Server、GitLab、Bitbucket Cloud 或 Azure Repos 上的程式碼開始。 然後您可以將程式碼的建置、測試和部署到 Microsoft Azure、Google Cloud Platform，或 Amazon Web Services 等等所有工作自動化。

**主要功能**
- 針對原生（Android、iOS 和 Windows）和跨平臺（Xamarin、Cordova 和 React Native）行動應用程式（除了 Microsoft 和非 Microsoft （node.js、JAVA）型伺服器），同時設定 CI 伺服器的簡化工作體驗技術.
- **任何語言、平臺和雲端**-組建、測試及部署 node.js、Python、JAVA、PHP、Ruby、Go、C/C++、 C#、Android 和 iOS 應用程式。 在 Linux、macOS 和 Windows 上同時執行。 部署至雲端提供者，例如 Azure、AWS 和 GCP。 透過 Beta 頻道和應用程式商店散發行動應用程式。
- **原生容器支援**-輕鬆建立新的容器，並將其推送至任何登錄。 將容器部署至獨立主機或 Kubernetes。
- **Advanced** workflow-簡單的組建連結和多階段組建。 支援 YAML、測試整合、發行閘道、報告等等。
- **可擴充**-使用由社區建立的一系列組建、測試和部署工作–數百個從時差到 SonarCloud 的延伸模組。 您甚至可以從其他 CI 系統（例如 Jenkins）進行部署，並使用 webhook 和 REST Api 來協助您整合
- 適用于公用和私人存放庫的**免費雲端託管組建**。
- **支援對其他雲端廠商**（例如 Amazon Web Services、Google Cloud Platform 等）進行部署。

**參考**
- [Azure Pipelines 指南入門](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [開始使用 Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [快速入門](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

若要為您的應用程式組建選擇正確的服務，請遵循比較[App Center 組建與 Azure Pipelines](/appcenter/build/choose-between-services)的相關文章。
