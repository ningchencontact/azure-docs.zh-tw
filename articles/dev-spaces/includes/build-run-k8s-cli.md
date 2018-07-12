---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: fbbd66dea73747acaf1c267f7d3ba7b1bb17baa2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728865"
---
## <a name="build-and-run-code-in-kubernetes"></a>在 Kubernetes 中建置及執行程式碼
讓我們來執行程式碼！ 在終端機視窗中，從**根程式碼資料夾**中執行 webfrontend 這個命令：

```cmd
azds up
```

請留意命令的輸出，您會在進行時注意到幾件事：
- 原始程式碼會同步至 Azure 中的開發人員空間。
- 容器映像內建在 Azure 中，如同程式碼資料夾中的 Docker 資產所指定。
- 系統會建立 Kubernetes 物件，它會利用程式碼資料夾中 Helm 圖表所指定的容器映像。
- 容器端點的相關資訊隨即顯示。 在我們的案例中，我們預期是公用 HTTP URL。
- 假設上述階段成功完成，您應該會在容器啟動時開始看到 `stdout` (和 `stderr`) 輸出。

> [!Note]
> 這些步驟在 `up` 命令第一次執行時會花費較長的時間，但是後續執行應該會更快。

### <a name="test-the-web-app"></a>測試 Web 應用程式
請掃描主控台輸出以取得 `up` 命令所建立的公用 URL 相關資訊。 它會在表單中： 

`Service 'webfrontend' port 'http' is available at <url>` 

在瀏覽器視窗中開啟此 URL，您應該會看到 Web 應用程式載入。 當容器執行時，`stdout` 和 `stderr` 輸出會串流至終端機視窗。

> [!Note]
> 第一次執行時，可能需要數分鐘的時間才能備妥公用 DNS。 如果無法解析公用 URL，您可以使用主控台輸出中顯示的替代 http://localhost:<portnumber> URL。 如果您使用 localhost URL，容器可能看起來像在本機執行，但實際是在 AKS 中執行。 為了方便您操作以及與本機電腦上的服務互動，Azure 開發人員空間會建立暫存的 SSH 通道，連到在 Azure 中執行的容器。 當 DNS 記錄備妥時，您可以返回且稍後嘗試公用 URL。
