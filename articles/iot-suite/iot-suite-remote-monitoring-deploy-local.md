---
title: 本機部署遠端監視解決方案 - Azure | Microsoft Docs
description: 本教學課程會示範如何將遠端監視所預先設定的解決方案部署到本機電腦，以進行測試和開發。
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>在本機部署遠端監視所預先設定的解決方案

本文會示範如何將遠端監視所預先設定的解決方案部署到本機電腦，以進行測試和開發。 這種方法會將微服務部署到本機 Docker 容器，並在雲端中使用 IoT 中樞、Cosmos DB 和 Azure 儲存體服務。 您可以使用預先設定的解決方案 (PCS) CLI 來部署 Azure 雲端服務。

## <a name="prerequisites"></a>先決條件

若要部署遠端監視預先設定之解決方案所使用的 Azure 服務，您需要一個作用中的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

若要完成本機部署，您必須在本機開發機器上安裝下列工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) - 此軟體是 PCS CLI 的必要條件。
* PCS CLI

> [!NOTE]
> 這些工具可在許多平台上取得，包括 Windows、Linux 及 iOS。

### <a name="install-the-pcs-cli"></a>安裝 PCS CLI

若要安裝 CLI，請在您的命令列環境中執行下列命令：

```cmd/sh
npm install iot-solutions -g
```

如需有關 CLI 的詳細資訊，請參閱 [如何使用 CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) \(英文\)。

## <a name="deploy-the-azure-services"></a>部署 Azure 服務

雖然本文會示範如何在本機執行微服務，但它們會仰賴雲端中執行的三個 Azure 服務。 您可以透過 Azure 入口網站或使用 PCS CLI 手動部署這些 Azure 服務。 本文說明如何使用 `pcs` 工具。

### <a name="sign-in-to-the-cli"></a>登入 CLI

您必須依照下列方式使用 CLI 來登入 Azure 訂用帳戶，才能部署預先設定的解決方案：

```cmd/sh
pcs login
```

請依照畫面上的指示來完成登入程序。

### <a name="run-a-local-deployment"></a>執行本機部署

使用下列命令來啟動本機部署：

```cmd/pcs
pcs -s local
```

指令碼會提示您輸入下列資訊：

* 解決方案名稱。
* 要使用的 Azure 訂用帳戶。
* 要使用之 Azure 資料中心的位置。

指令碼會在您 Azure 訂用帳戶的資源群組中建立 IoT 中樞執行個體、Cosmos DB 執行個體和 Azure 儲存體帳戶。 資源群組的名稱是您在執行 `pcs` 工具時選擇之解決方案的名稱。

指令碼需要幾分鐘的時間來執行。 完成之後，您會看到下列訊息：`Copy the following environment variables to /scripts/local/.env file:`。 複製訊息後面的環境變數定義，以在稍後的步驟中使用。

## <a name="download-the-source-code"></a>下載原始程式碼

遠端監視原始程式碼存放庫包含您在下載、設定及執行內含微服務之 Docker 映像時所需的 Docker 組態檔。 若要複製存放庫，請巡覽至您本機電腦上的適當資料夾，並執行下列其中一個命令：

若要安裝微服務的 Java 實作，請執行：

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

若要安裝微服務的 .Net 實作，請執行：

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

這些命令會下載所有微服務的原始程式碼。 雖然您在 Docker 中執行微服務時不需要原始程式碼，但如果您稍後打算修改預先設定的解決方案，並在本機測試您的變更，原始程式碼就會很有用。

## <a name="run-the-microservices-in-docker"></a>在 Docker 中執行微服務

若要在 Docker 中執行微服務，請先編輯您存放庫本機複本中的 **scripts\\local\\.env** 檔案。 將檔案的整個內容取代為您先前執行 `pcs` 命令時所記下的環境變數定義。 這些環境變數可讓 Docker 容器中的微服務連線到 `pcs` 工具所建立的 Azure 服務。

若要執行預先設定的解決方案，請巡覽至您命令列環境中的 **scripts\local** 資料夾，然後執行下列命令：

```cmd\sh
docker-compose up
```

您第一次執行此命令時，Docker 會下載 Docker 中樞的微服務映像，以在本機建置容器。 在後續執行時，Docker 則會立即執行容器。

您可以使用個別殼層來檢視容器中的記錄。 請先使用 `docker ps -a` 命令來尋找容器識別碼。 然後使用 `docker logs {container-id} --tail 1000` 來檢視指定容器的最後 1000 個記錄項目。

若要存取遠端監視解決方案儀表板，請巡覽至您瀏覽器中的 [http://localhost:8080](http://localhost:8080)。

## <a name="tidy-up"></a>整理一下

為避免不必要的費用，當您完成測試時，請從您的 Azure 訂用帳戶中移除雲端服務。 若要移除服務，最簡單的方式是使用 Azure 入口網站來刪除 `pcs` 工具所建立的資源群組。

使用 `docker-compose down --rmi all` 命令來移除 Docker 映像，並釋放您本機電腦上的空間。 您也可以將您從 GitHub 複製原始程式碼時所建立的遠端監視存放庫本機副本刪除。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定預先設定的解決方案
> * 部署預先設定的解決方案
> * 登入預先設定的解決方案

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](./iot-suite-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->