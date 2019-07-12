---
title: Visual Studio Code 以 Azure 開發人員空格的運作方式
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code 以 Azure 開發人員空格的運作方式
keywords: Azure 開發人員空格、 開發人員空格、 Docker、 Kubernetes、 Azure、 AKS，Azure Kubernetes 服務、 容器
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712146"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code 以 Azure 開發人員空格的運作方式

您可以使用 Visual Studio Code 和[Azure 開發人員空間延伸模組][azds-extension]準備、 執行和偵錯您的服務與 Azure 開發人員的空間。 使用 Visual Studio Code 和 Azure 開發人員空間延伸模組，您可以：

* 執行與偵錯在 AKS 中的服務產生的資產
* 開發人員空間中執行 Java、 Node.js 和.NET Core 服務
* 直接偵錯您的開發人員空間中執行的 Java、 Node.js 和.NET Core 服務

## <a name="generate-assets"></a>產生資產

Visual Studio Code 和 Azure 開發人員空間延伸模組產生您專案的下列資產：

* 使用 Maven 的 Java 應用程式、 Node.js 應用程式，和.NET Core 應用程式的 Dockerfile
* 適用於幾乎任何語言，使用 Dockerfile 的 helm 圖表
* `azds.yaml`檔案，這是[Azure 開發空間的組態檔][azds-yaml]為您的專案
* A`.vscode`資料夾中，使用 Visual Studio 程式碼啟動的組態的專案，以使用 Maven 的 Java 應用程式、 Node.js 應用程式和.NET Core 應用程式

Dockerfile、 Helm 圖表，以及`azds.yaml`檔案會產生執行時的相同資產`azds prep`。 這些檔案也可在 Visual Studio code 外部 AKS，以執行您的專案，例如執行`azds up`。 `.vscode`資料夾只供 Visual Studio code 在 AKS 中執行您的專案，從 Visual Studio Code。

## <a name="run-your-service-in-aks"></a>在 AKS 中執行您的服務

為您的專案產生的資產之後，您可以在現有的開發人員空間中執行您的 Java、 Node.js 和.NET Core 服務，從 Visual Studio Code 項目。 在 *偵錯*頁面的 Visual Studio Code 中，您可以叫用的啟動設定`.vscode`目錄來執行您的專案。

您必須建立您的 AKS 叢集，並在 Visual Studio Code 外部叢集中啟用 Azure 開發人員的空格。 例如，您可以使用 Azure CLI 或 Azure 入口網站執行這項設定。 您可以重複使用現有的 Dockerfile、 Helm 圖表，以及`azds.yaml`建立外部 Visual Studio Code 中，例如執行所產生的資產檔案`azds prep`。 如果您不要重複使用 Visual Studio Code 外部產生的資產，您仍必須能夠`.vscode`目錄。 這`.vscode`目錄可由 Visual Studio code 和 Azure 開發人員空間延伸模組，並不會覆寫您現有的資產。

針對.NET Core 專案，您必須擁有[C#延伸模組][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] ，以及已安裝[Maven 安裝並設定][maven]執行您的 Java從 Visual Studio Code 的服務。

## <a name="debug-your-service-in-aks"></a>您在 AKS 中的服務進行偵錯

啟動您的專案之後，您可以偵錯您直接從 Visual Studio Code 中開發的空間執行的 Java、 Node.js 和.NET Core 服務。 中的啟動設定`.vscode`目錄提供開發人員空間中已啟用偵錯執行服務的其他偵錯資訊。 Visual Studio 程式碼也將附加至您的開發人員分享空間中執行的容器中的偵錯程序可讓您設定中斷點、 檢查變數，以及執行其他偵錯作業。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>使用 Visual Studio Code 以 Azure 的 Dev 空格

您可以看到 Visual Studio Code 和 Azure 開發人員空間延伸模組使用下列快速入門中的 Azure 開發人員空格：

* [使用 Java 進行開發][quickstart-java]
* [使用.NET 進行開發][quickstart-netcore]
* [使用 Node.js 進行開發][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md