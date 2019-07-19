---
title: Visual Studio Code 如何與 Azure Dev Spaces 搭配運作
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Visual Studio Code 如何與 Azure Dev Spaces 搭配運作
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297861"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Visual Studio Code 如何與 Azure Dev Spaces 搭配運作

您可以使用 Visual Studio Code 和[Azure Dev Spaces 延伸][azds-extension]模組, 透過 Azure Dev Spaces 來準備、執行和偵測您的服務。 使用 Visual Studio Code 和 Azure Dev Spaces 延伸模組, 您可以:

* 產生資產, 以便在 AKS 中執行和偵錯工具服務
* 在開發人員空間中執行 JAVA、node.js 和 .NET Core 服務
* 直接對在開發人員空間中執行的 JAVA、node.js 和 .NET Core 服務進行偵錯工具

## <a name="generate-assets"></a>產生資產

Visual Studio Code 和 Azure Dev Spaces 延伸模組會為您的專案產生下列資產:

* 使用 Maven、node.js 應用程式和 .NET Core 應用程式的 JAVA 應用程式 dockerfile
* 使用 Dockerfile Helm 幾乎任何語言的圖表
* 檔案, 這是您專案的[Azure Dev Spaces 設定檔][azds-yaml] `azds.yaml`
* Visual Studio Code 為使用 Maven、node.js 應用程式和 .net Core 應用程式的 JAVA 應用程式啟動專案設定的資料夾`.vscode`

Dockerfile、Helm 圖表和`azds.yaml`檔案是執行`azds prep`時所產生的相同資產。 這些檔案也可以在 Visual Studio 程式碼之外使用, 以在 AKS 中執行您的專案, `azds up`例如執行。 `.vscode`資料夾僅供 Visual Studio 的程式碼使用, 以便從 Visual Studio Code 在 AKS 中執行您的專案。

## <a name="run-your-service-in-aks"></a>在 AKS 中執行您的服務

產生專案的資產之後, 您可以從 Visual Studio Code 在現有的開發人員空間中執行 JAVA、node.js 和 .NET Core 服務。 在 Visual Studio Code 的 [*調試*程式] 頁面中, 您可以從`.vscode`目錄叫用啟動設定來執行專案。

您必須建立 AKS 叢集, 並在 Visual Studio Code 以外的叢集中啟用 Azure Dev Spaces。 例如, 您可以使用 Azure CLI 或 Azure 入口網站來執行此設定。 您可以重複使用現有的 dockerfile、Helm 圖表`azds.yaml`和 Visual Studio Code 外部建立的檔案, 例如執行所產生`azds prep`的資產。 如果您重複使用在 Visual Studio Code 外部產生的資產, 您仍然需要擁有`.vscode`目錄。 此`.vscode`目錄可由 Visual Studio 程式碼和 Azure Dev Spaces 延伸模組來重新產生, 且不會覆寫現有的資產。

針對 .net Core 專案, 您必須安裝[ C#延伸][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension]模組, 以及[安裝 Maven 並設定][Maven] , 以從 Visual Studio Code 執行 JAVA 服務。

## <a name="debug-your-service-in-aks"></a>在 AKS 中對服務進行偵錯工具

啟動您的專案之後, 您可以直接從 Visual Studio Code, 將在開發人員空間中執行的 JAVA、node.js 和 .NET Core 服務進行偵錯工具。 `.vscode`目錄中的啟動設定會提供額外的偵錯工具資訊, 以在開發人員空間中啟用偵測功能的情況下執行服務。 Visual Studio Code 也會附加到開發人員空間中執行中容器內的偵錯工具, 讓您可以設定中斷點、檢查變數, 以及執行其他的偵錯工具。


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>搭配 Azure Dev Spaces 使用 Visual Studio Code

在下列快速入門中, 您可以看到 Visual Studio Code 和 Azure Dev Spaces 延伸模組使用 Azure Dev Spaces:

* [使用 JAVA 進行開發][quickstart-java]
* [使用 .NET 進行開發][quickstart-netcore]
* [使用 node.js 進行開發][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
