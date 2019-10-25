---
title: 使用 Azure Functions 和其他服務組建無伺服器行動應用程式後端
description: 深入瞭解計算服務，以建立穩固、無伺服器的行動應用程式後端。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795857"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>使用計算服務建立行動後端元件
每個行動應用程式都需要一個可負責資料儲存、商務邏輯和安全性的後端。 管理要裝載和執行後端程式碼的基礎結構，需要您調整規模、布建及調整一堆伺服器、管理所牽涉的作業系統更新和硬體、套用安全性修補程式，然後監視所有這些基礎結構元件的效能，可用性和容錯。 這是因為當開發人員沒有伺服器可管理、作業系統或相關的軟體/硬體更新需要管理時，就能派上用場。 它節省了許多開發人員的時間和成本，這表示在建立應用程式時，更快上市和專注的能源。

## <a name="benefits-of-compute"></a>計算的優點
- 伺服器的抽象概念：不需要擔心裝載、修補和安全性，只要讓開發人員專注于程式碼即可。
- 立即且有效率的調整可確保資源會自動布建，或視您需要的任何規模而定。
- 高可用性和容錯。
- 微計費可確保只有在您的程式碼實際執行時，才會向您收取費用。
- 以您選擇的語言撰寫在雲端中執行的程式碼。

使用下列服務，在您的行動應用程式中啟用無伺服器計算功能。

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/)是事件驅動的計算體驗，可讓您以您選擇的程式設計語言來執行程式碼，而不需要擔心伺服器。 開發人員不需要管理應用程式或基礎結構，就可以在上執行。 函式會依需求進行調整，而您只需要為程式碼執行的時間付費。 Azure Functions 是實作為行動應用程式 API 的絕佳方式，因為它們非常容易執行和維護，而且可透過 HTTP 存取。

**主要功能**
- **事件驅動和可擴充**，讓開發人員可以使用**觸發程式和**系結來定義叫用函式的時機，以及它所連接的資料。
- **自備相依性** - Functions 支援 NuGet 和 NPM，以便您使用您最愛的程式庫。
- **整合式安全性**可讓您使用 OAuth 提供者（例如 Azure Active Directory、Facebook、Google、Twitter 和 Microsoft 帳戶）保護 HTTP 觸發的函式。
- **簡化**與不同[Azure 服務](/azure/azure-functions/functions-overview#integrations)和軟體即服務（SaaS）供應專案的整合。
- **彈性的開發**可讓您直接在入口網站中撰寫函式的程式碼，或透過 GitHub、Azure DevOps Services 和其他支援的開發工具來設定持續整合和部署程式碼。
- 函式執行時間是**開放原始**碼，並可在[GitHub](https://github.com/azure/azure-webjobs-sdk-script)上取得。
-  **增強的開發體驗**，讓開發人員可以使用其慣用的編輯器或便於使用的 web 介面，以整合式工具和內建的 DevOps 功能進行監視，以在本機進行程式碼、測試及進行偵錯工具。
- **各種程式設計語言和裝載選項**-使用C#、Node.js、JAVA、JAVAscript 或 Python 進行開發。
- **使用即付費價格模式** - 只對執行您的程式碼所花的時間付費。

**參考**
- [Azure 入口網站](https://portal.azure.com)
- [Documentation](/azure/azure-functions/)
- [Azure Functions 開發人員指南](/azure/azure-functions/functions-reference)
- [快速入門](/azure/azure-functions/functions-create-first-function-vs-code)
- [範例](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service 方案
[Azure App Service](https://azure.microsoft.com/services/app-service/)Azure App Service 可讓您建立和裝載 web 應用程式，並以您選擇的程式設計語言來 RESTful Api，而不需要管理基礎結構。 它提供自動調整及高度可用性，支援 Windows 和 Linux，並可從 GitHub、Azure DevOps 或任何 Git 存放庫啟用自動部署。

**主要功能**
- **多種語言和**架構支援 ASP.NET、ASP.NET Core、JAVA、Ruby、NODE.JS、PHP 或 Python。 您也可以將 PowerShell 和其他腳本或可執行檔當做背景服務來執行。
- **DevOps 優化**-使用 Azure DevOps、GitHub、BitBucket、Docker Hub 或 Azure Container Registry 設定持續整合和部署。 使用 Azure PowerShell 或跨平台命令列介面 (CLI)，在 App Service 中管理您的應用程式。
- **具有高可用性的全球規模**，可手動或自動相應增加或放大。
- 連線**至 saas 平臺和內部部署資料**，以針對企業系統（例如 SAP）、SaaS 服務（例如 Salesforce）和網際網路服務（例如 Facebook）的50個連接器進行選擇。 使用混合式連接和 Azure 虛擬網路存取內部部署資料。
- **安全性與合規性**-AZURE APP SERVICE 為 ISO、SOC 和 PCI 相容。 使用 Azure Active Directory 或社交登入（Google、Facebook、Twitter 和 Microsoft）驗證使用者。 建立 IP 位址限制和管理服務身分識別。
- **應用程式範本**，可從 Azure Marketplace 中的廣泛應用程式範本清單中進行選擇，例如 WordPress、Joomla 和 Drupal。
- **Visual Studio**與 Visual Studio 的專用工具整合，可簡化建立、部署和調試作業的工作。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Documentation](/azure/app-service/)
- [快速入門](/azure/app-service/app-service-web-get-started-dotnet)
- [範例](/azure/app-service/samples-cli)
- [教學課程](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 可以管理裝載 Kubernetes 的環境；因此，您不需具備容器協調流程專業知識，就能快速、輕鬆地部署及管理容器化應用程式。 也可透過佈建、升級與依需求調整資源，以無需讓應用程式離線的方式來消除進行中作業及維護之間的界線。 

**主要功能**
- **輕鬆地將現有的應用程式遷移**至容器，並在 AKS 內執行。
- 簡化以微服務為基礎的應用程式**部署和管理**。
- **保護 DevOps 的 AKS** ，以達到速度和安全性之間的平衡，並更快速地提供程式碼。
- **輕鬆地使用 AKS 和 ACI**來布建在 ACI 中布建的 pod，並在幾秒內啟動。
- **IoT 裝置部署和**隨選管理。
- 使用 TensorFlow 和 KubeFlow 等工具進行**機器學習模型定型**。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Documentation](/azure/aks/)
- [快速入門](/azure/aks/kubernetes-walkthrough-portal)
- [教學課程](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure 容器執行個體
對於可在隔離容器中運作的任何案例，包括簡單的應用程式、工作自動化和組建作業， [Azure 容器實例（ACI）](https://azure.microsoft.com/services/container-instances/)是絕佳的解決方案。 快速開發應用程式，而不需要管理 Vm。

**主要功能**
- **快速啟動時間**，因為 ACI 可以在幾秒內啟動 Azure 中的容器，而不需要布建和管理 vm。
- **公用 IP 連線能力和自訂 DNS 名稱**。
- 可確保您的應用程式在容器中會如同在 VM 中隔離的**虛擬機器層級安全性**。
- 藉由允許 CPU 核心和記憶體的確切規格，來達到最佳使用率的**自訂大小**。 您可根據所需的數量付費並採用以秒計費方式，因此能夠根據實際的需求來微調您的費用。
- **持續性儲存體**：若要取得和保存狀態，ACI 提供 Azure 檔案儲存體共用的直接裝載。
- 使用相同 API 排程的**Linux 和 Windows 容器**。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Documentation](/azure/container-instances/)
- [快速入門](/azure/container-instances/container-instances-quickstart-portal)
- [範例](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [教學課程](/azure/container-instances/container-instances-tutorial-prepare-app)