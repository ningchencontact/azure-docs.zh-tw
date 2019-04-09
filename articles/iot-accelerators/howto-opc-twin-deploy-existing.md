---
title: 如何將 Azure IoT OPC UA 裝置管理模組部署至現有的專案 |Microsoft Docs
description: 如何部署至現有的專案的 OPC 對應項。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dcf6acca344fe2a34fdc48fe89c5a1ee62b10b23
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59255881"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>部署至現有的專案的 OPC 對應項

OPC 裝置對應項的模組會在 IoT Edge 上執行，並提供數個 edge 服務的 OPC 裝置對應項和登錄服務。 

OPC 裝置對應項微服務有助於 factory 運算子與透過 OPC 對應項 IoT Edge 模組在工廠的 OPC UA 伺服器裝置之間的通訊。 微服務會公開透過其 REST API （瀏覽、 讀取、 寫入及執行） 的 OPC UA 服務。 

OPC UA 裝置登錄的微服務會提供已註冊的 OPC UA 應用程式和其端點的存取。 操作員和系統管理員可以註冊和取消註冊新的 OPC UA 應用程式及瀏覽現有的項目，包括其端點。 除了應用程式和管理端點，「 登錄 」 服務也會分類已註冊的 OPC 裝置對應項 IoT Edge 模組。 服務 API 可讓您控制 edge 模組的功能，例如啟動或停止伺服器探索 （掃描服務），或啟動新的 「 端點 」 對應項，可以使用 OPC 對應項微服務來存取。

模組的核心是監督員身分識別。 監督員管理端點對應項，其對應至使用對應的 OPC UA 登錄 API 啟動的 OPC UA 伺服器端點。 此端點的對應項會轉譯從 OPC 對應項微服務在雲端中執行 OPC UA 二進位訊息，會透過可設定狀態的安全通道傳送至受管理的端點收到的 OPC UA JSON。 監督員也會提供探索服務，傳送給 OPC UA 裝置上架服務以進行處理，其中這些事件會導致更新至 OPC UA 登錄的裝置探索事件。  這篇文章會示範如何將 OPC 對應項模組部署到現有的專案。 

> [!NOTE]
> 如需有關部署詳細資料和指示的詳細資訊，請參閱 GitHub[存放庫](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>必要條件

請確定您具有 PowerShell 及[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)安裝擴充功能。   如果您不這麼做還，複製這個 GitHub 存放庫。  開啟命令提示字元或終端機並執行：

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>將工業 IoT 服務部署至 Azure

1. 開啟命令提示字元或終端機中執行：

   ```bash
   deploy
   ```

2. 遵循提示來指派至網站的部署的資源群組的名稱和名稱。   指令碼會部署微服務和您的 Azure 訂用帳戶中的資源群組及其 Azure 平台相依性。  指令碼也會支援 OAUTH 型驗證 Azure Active Directory (AAD) 租用戶中註冊應用程式。  部署需要幾分鐘的時間。  您會看到成功部署解決方案後範例：

   ![工業 IoT OPC 對應項將部署到現有的專案](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   輸出會包含公用端點的 URL。 

3. 一旦順利完成指令碼，請選取您是否想要儲存.env 檔案。  如果您想要連線到雲端端點使用主控台之類的工具，或部署適用於開發和偵錯模組，您會需要.env 環境檔案。

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

請確定您使用簡短且簡單的資源群組名稱。  若要取得名稱資源因此它必須符合命名需求的資源也會使用的名稱。  

### <a name="website-name-already-in-use"></a>已在使用中的網站名稱

您可在網站的名稱已在使用。  如果您遇到這個錯誤時，您需要使用不同的應用程式的名稱。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 註冊

部署指令碼會嘗試註冊 Azure Active Directory 中的兩個 AAD 應用程式。  根據您選取的 AAD 租用戶的權限，部署可能會失敗。 有兩個選項：

1. 如果您選擇的 AAD 租用戶的租用戶清單時，重新啟動指令碼，然後從清單選擇不同。
2. 或者，部署私用的 AAD 租用戶，另一個訂用帳戶中，重新啟動指令碼，並選取要使用它。

> [!WARNING]
> 永遠不會繼續使用不驗證。  如果您選擇這樣做，任何人都可以從網際網路未經驗證的認證來存取 OPC 裝置管理端點。   您可以隨時[「 本機 」 的部署選項](howto-opc-twin-deploy-dependencies.md)以親身。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署-全方位工業 IoT 服務示範

您也可以部署-全方位示範而不是服務和相依性。  在一個示範所有包含三個的 OPC UA 伺服器、 OPC 對應項模組、 所有微服務和 Web 應用程式的範例。  它被供示範之用。

1. 請確定您已複製的存放庫 （如上所示）。 開啟命令提示字元或終端機根目錄中的存放庫並執行：

   ```bash
   deploy -type demo
   ```

2. 遵循提示以將新的名稱指派給資源群組和網站的名稱。  一旦成功部署指令碼會顯示 web 應用程式端點的 URL。

## <a name="deployment-script-options"></a>部署指令碼選項

指令碼會使用下列參數：

```bash
-type
```

部署 （虛擬機器本機，示範） 類型

```bash
-resourceGroupName
```

可以是現有或新的資源群組的名稱。

```bash
-subscriptionId
```

選擇性，將部署資源的訂用帳戶識別碼。

```bash
-subscriptionName
```

或訂用帳戶名稱。

```bash
-resourceGroupLocation
```

選擇性，資源群組的位置。 如果指定，會嘗試在這個位置建立新的資源群組。

```bash
-aadApplicationName
```

向下的 AAD 應用程式名稱。 

```bash
-tenantId
```

若要使用的 AAD 租用戶。

```bash
-credentials
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何將 OPC 對應項部署至現有的專案，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [OPC 用戶端和 OPC PLC 間的安全通訊](howto-opc-vault-deploy-existing-client-plc-communication.md)
