---
title: 搭配 Azure Logic Apps 使用乙太坊區塊鏈連接器
description: 搭配 Azure Logic Apps 使用乙太坊區塊鏈連接器，以觸發智慧合約函式並回應智慧合約事件。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: bb23d6b9b42e1c51646765255870a14a1b5d39f7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579945"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>搭配 Azure Logic Apps 使用乙太坊區塊鏈連接器

搭配[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)使用[乙太坊區塊鏈連接器](https://docs.microsoft.com/connectors/blockchainethereum/)來執行智慧合約動作，並回應智慧合約事件。 例如，假設您想要建立以 REST 為基礎的微服務，以傳回區塊鏈總帳中的資訊。 藉由使用邏輯應用程式，您可以接受查詢儲存在區塊鏈總帳中之資訊的 HTTP 要求。

## <a name="prerequisites"></a>必要條件

完成選用的必要條件[快速入門：使用 Visual Studio Code 連接到 Azure 區塊鏈 Service 聯盟網路](connect-vscode.md)。 本快速入門會引導您安裝[Azure 區塊鏈開發套件以乙太坊](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)和設定您的區塊鏈開發環境。

## <a name="create-a-logic-app"></a>建立邏輯應用程式

當您需要整合系統和服務時，Azure Logic Apps 可協助您排程和自動化商務程式和工作流程。 首先，您要建立使用乙太坊區塊鏈連接器的邏輯。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 [建立資源] > [整合] > [邏輯應用程式]。
1. 在 [**建立邏輯應用程式**] 下，提供建立邏輯應用程式之位置的詳細資料。 在完成作業後，選取 [建立]。

    如需建立邏輯應用程式的詳細資訊，請參閱[使用 Azure Logic Apps 建立自動化工作流程](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在 Azure 部署您的應用程式之後，請選取您的邏輯應用程式資源。
1. 在 Logic Apps 設計工具的 [**範本**] 底下，選取 [**空白邏輯應用程式**]。

每個邏輯應用程式都必須使用觸發程序啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎會建立邏輯應用程式執行個體，啟動並執行您的工作流程。

乙太坊區塊鏈連接器有一個觸發程式和數個動作。 您所使用的觸發程式或動作取決於您的案例。

如果您的工作流程：

* 在區塊鏈上發生事件時觸發，請[使用事件觸發](#use-the-event-trigger)程式。
* 查詢或部署智慧合約，[使用動作](#use-actions)。
* 遵循常見的案例，[使用開發人員套件來產生工作流程](#generate-a-workflow)。

## <a name="use-the-event-trigger"></a>使用事件觸發程式

當您想要讓邏輯應用程式在智慧合約事件發生之後執行時，請使用乙太坊區塊鏈事件觸發程式。 例如，您想要在呼叫智慧合約函數時傳送電子郵件。

1. 在 Logic Apps 設計工具中，選取 [乙太坊區塊鏈連接器]。
1. 從 [**觸發**程式] 索引標籤中，選取 [**當智慧合約事件發生時**]。
1. 變更或建立與 Azure 區塊鏈 Service[的 API](#create-an-api-connection)連線。
1. 輸入您要檢查事件之智慧合約的相關詳細資料。

    ![具有事件觸發程式屬性的 Logic Apps 設計工具](./media/ethereum-logic-app/event-properties.png)

    | 屬性 | 說明 |
    |----------|-------------|
    | **合約 ABI** | 合約應用程式二進位介面（ABI）會定義智慧合約介面。 如需詳細資訊，請參閱[取得合約 ABI](#get-the-contract-abi)。 |
    | **智慧合約位址** | 合約位址是乙太坊區塊鏈上的智慧合約目的地位址。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。 |
    | **事件名稱** | 選取要檢查的智慧合約事件。 事件會觸發邏輯應用程式。 |
    | **間隔**和**頻率** | 選取您想要檢查事件的頻率。 |

1. 選取 [ **儲存**]。

若要完成您的邏輯應用程式，您可以新增一個步驟，以根據乙太坊區塊鏈事件觸發程式來執行動作。 例如，傳送電子郵件。

## <a name="use-actions"></a>使用動作

當您想要邏輯應用程式在區塊鏈總帳上執行動作時，請使用乙太坊區塊鏈動作。 例如，您想要建立以 REST 為基礎的微服務，在對邏輯應用程式發出 HTTP 要求時呼叫智慧合約函式。

連接器動作需要觸發程式。 您可以使用乙太坊區塊鏈連接器動作作為觸發程式之後的下一個步驟，例如微服務的 HTTP 要求觸發程式。

1. 在 Logic Apps 設計工具中，選取觸發程式之後的 [**新增步驟**]。
1. 選取 [乙太坊區塊鏈連接器]。
1. 從 [**動作**] 索引標籤中，選取其中一個可用的動作。

    ![具有動作屬性的 Logic Apps 設計工具](./media/ethereum-logic-app/action-properties.png)

1. 變更或建立與 Azure 區塊鏈 Service[的 API](#create-an-api-connection)連線。
1. 視您選擇的動作而定，提供有關智慧合約函數的下列詳細資料。

    | 屬性 | 說明 |
    |----------|-------------|
    | **合約 ABI** | 合約 ABI 會定義智慧合約介面。 如需詳細資訊，請參閱[取得合約 ABI](#get-the-contract-abi)。 |
    | **合約位元組碼** | 已編譯的智慧合約位元組碼。 如需詳細資訊，請參閱[取得合約位元組](#get-the-contract-bytecode)程式碼。 |
    | **智慧合約位址** | 合約位址是乙太坊區塊鏈上的智慧合約目的地位址。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。 |
    | **智慧合約函數名稱** | 選取動作的智慧合約函數名稱。 此清單會從合約 ABI 中的詳細資料中填入。 |

    選取智慧合約函數名稱之後，您可能會看到函式參數的必要欄位。 輸入您的案例所需的值或動態內容。

您現在可以使用邏輯應用程式。 觸發邏輯應用程式事件時，會執行乙太坊區塊鏈動作。 例如，HTTP 要求觸發程式會執行乙太坊區塊鏈動作來查詢智慧合約狀態值。 此查詢會產生傳回值的 HTTP 回應。

## <a name="generate-a-workflow"></a>產生工作流程

乙太坊 Visual Studio Code 延伸模組的 Azure 區塊鏈開發套件可為常見案例產生邏輯應用程式工作流程。 提供四種案例：

* 將資料發行至 Azure SQL Database 實例
* 發佈至 Azure 事件方格或 Azure 服務匯流排實例的事件
* 報表發行
* 以 REST 為基礎的微服務

 Azure 區塊鏈開發套件使用 Truffle 來簡化區塊鏈開發。 若要根據智慧合約產生邏輯應用程式，您需要智慧合約的 Truffle 解決方案。 您也需要連線到您的 Azure 區塊鏈 Service 聯盟網路。 如需詳細資訊，請參閱[使用 Visual Studio Code 連接到 Azure 區塊鏈 Service 聯盟網路快速入門](connect-vscode.md)。

例如，下列步驟會根據快速入門**HelloBlockchain**智慧合約，產生以 REST 為基礎的微服務邏輯應用程式：

1. 在 [Visual Studio Code explorer] 提要欄位中，展開解決方案中的 [**合約**] 資料夾。
1. 以滑鼠右鍵按一下 [ **HelloBlockchain** ]，然後從功能表中選取 [**產生智慧合約的微服務**]。

    ![Visual Studio Code 窗格，其中包含 產生智慧合約的微服務 選項](./media/ethereum-logic-app/generate-logic-app.png)

1. 在命令選擇區中，選取 [**邏輯應用程式**]。
1. 輸入**合約位址**。 如需詳細資訊，請參閱[取得合約位址](#get-the-contract-address)。
1. 選取邏輯應用程式的 [Azure 訂用帳戶] 和 [資源群組]。

    邏輯應用程式設定和程式碼檔案會在**generatedLogicApp**目錄中產生。

1. 查看**generatedLogicApp/HelloBlockchain**目錄。 每個智慧合約函數、事件和屬性都有一個邏輯應用程式 JSON 檔案。
1. 開啟**generatedLogicApp/HelloBlockchain/服務/屬性。RequestMessage. logicapp json**檔案並複製內容。

    ![包含要複製之程式碼的 JSON 檔案](./media/ethereum-logic-app/requestmessage.png)

1. 在您的邏輯應用程式中，選取 [**邏輯應用程式代碼視圖**]。 以產生的邏輯應用程式 JSON 取代現有的 JSON。

    ![具有新取代應用程式代碼的邏輯應用程式代碼視圖](./media/ethereum-logic-app/code-view.png)

1. 選取 [**設計**工具] 以切換至設計工具視圖。
1. 邏輯應用程式包含案例的基本步驟。 不過，您必須更新乙太坊區塊鏈連接器的設定詳細資料。
1. 選取 [**連線] 步驟，並**變更或建立與 Azure 區塊鏈 SERVICE[的 API](#create-an-api-connection)連線。

    ![具有連接選項的設計工具視圖](./media/ethereum-logic-app/microservice-logic-app.png)

1. 您現在可以使用邏輯應用程式。 若要測試以 REST 為基礎的微服務，請對邏輯應用程式要求 URL 發出 HTTP POST 要求。 從 [**收到 HTTP 要求時**] 步驟複製**HTTP POST URL**內容。

    ![具有 HTTP POST URL 的 Logic Apps 設計工具窗格](./media/ethereum-logic-app/post-url.png)

1. 使用捲曲來建立 HTTP POST 要求。 以上一個步驟中的 URL 取代預留位置文字 *\<HTTP POST url\>* 。

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    捲曲命令會傳回邏輯應用程式的回應。 在此情況下，回應是來自**RequestMessage**智慧合約函數的輸出。

    ![來自 RequestMessage 智慧合約函式的程式碼輸出](./media/ethereum-logic-app/curl.png)

如需使用開發工具組的詳細資訊，請參閱[乙太坊 wiki 的 Azure 區塊鏈開發套件頁面](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)。

## <a name="create-an-api-connection"></a>建立 API 連線

乙太坊區塊鏈連接器需要與區塊鏈的 API 連線。 您可以針對多個邏輯應用程式使用 API 連接器。 有些屬性是必要的，有些則取決於您的案例。

> [!IMPORTANT]
> 在區塊鏈上建立交易時，需要私密金鑰或帳戶位址和密碼。 只需要一種驗證形式。 您不需要同時提供私密金鑰和帳戶詳細資料。 查詢合約不需要交易。 如果您使用查詢合約狀態的動作，則不需要私密金鑰或帳戶位址和密碼。

為了協助您設定與 Azure 區塊鏈服務成員的連線，下列清單有可能的屬性，視您的案例而定。

| 屬性 | 說明 |
|----------|-------------|
|**連線名稱** | API 連線的名稱。 必要。 |
|**乙太坊 RPC 端點** | Azure 區塊鏈 Service 交易節點的 HTTP 位址。 必要。 如需詳細資訊，請參閱[取得 RPC 端點](#get-the-rpc-endpoint)。 |
|**私密金鑰** | 乙太坊帳戶私密金鑰。 交易需要私密金鑰或帳戶位址和密碼。 如需詳細資訊，請參閱[取得私密金鑰](#get-the-private-key)。 |
|**帳戶位址** | Azure 區塊鏈服務的成員帳戶位址。 交易需要私密金鑰或帳戶位址和密碼。 如需詳細資訊，請參閱[取得帳戶位址](#get-the-account-address)。 |
|**帳戶密碼** | 帳戶密碼會在您建立成員時設定。 如需重設密碼的詳細資訊，請參閱[乙太坊帳戶](consortium.md#ethereum-account)。|

## <a name="get-the-rpc-endpoint"></a>取得 RPC 端點

需要 Azure 區塊鏈 Service RPC 端點位址，才能連線到區塊鏈網路。 您可以使用乙太坊或 Azure 入口網站的 Azure 區塊鏈開發套件來取得端點位址。

**若要使用開發工具組：**

1. 在 Visual Studio Code 的 [ **Azure 區塊鏈 Service** ] 底下，以滑鼠右鍵按一下聯盟。
1. 選取 [**複製 RPC 端點位址**]。

    ![Visual Studio Code 窗格中，顯示具有「複製 RPC 端點」位址選擇的聯盟](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 端點會複製到您的剪貼簿。

**若要使用 Azure 入口網站：**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 移至您的 Azure 區塊鏈服務成員。 選取 [交易節點] 和預設交易節點連結。

    ![具有（預設節點）選取範圍的交易節點頁面](./media/ethereum-logic-app/transaction-nodes.png)

1. 選取 **連接字串** > **存取金鑰**。
1. 從 [ **HTTPs （存取金鑰1）** ] 或 [ **HTTPs （存取金鑰2）** ] 複製端點位址。

    ![使用連接字串存取金鑰 Azure 入口網站](./media/ethereum-logic-app/connection-string.png)

    RPC 端點是 HTTPS URL，其中包括 Azure 區塊鏈 Service 成員交易節點的位址和存取金鑰。

## <a name="get-the-private-key"></a>取得私密金鑰

將交易傳送至區塊鏈時，您可以使用乙太坊帳戶的私密金鑰來進行驗證。 您的乙太坊帳戶的公開和私密金鑰是由12個字的助憶鍵所產生。 當您連接到 Azure 區塊鏈 Service 聯盟成員時，乙太坊的 Azure 區塊鏈開發套件會產生助憶鍵。 您可以使用開發工具組擴充功能來取得端點位址。

1. 在 Visual Studio Code 中，開啟命令選擇區（F1）。
1. 選取 [ **Azure 區塊鏈：取出私密金鑰**]。
1. 選取連接到聯盟成員時所儲存的助憶鍵。

    ![命令選擇區，具有選取助憶鍵的選項](./media/ethereum-logic-app/private-key.png)

    私密金鑰會複製到您的剪貼簿。

## <a name="get-the-account-address"></a>取得帳戶位址

當您將交易傳送至區塊鏈時，可以使用成員帳戶和密碼來進行驗證。 當您建立成員時，就會設定密碼。

1. 在 Azure 入口網站中，移至您的 Azure 區塊鏈 Service [總覽] 頁面。
1. 複製**成員帳戶**位址。

    ![具有成員帳戶位址的總覽頁面](./media/ethereum-logic-app/member-account.png)

如需帳戶位址和密碼的詳細資訊，請參閱[乙太坊帳戶](consortium.md#ethereum-account)。

## <a name="get-the-contract-abi"></a>取得合約 ABI

合約 ABI 會定義智慧合約介面。 其中描述如何與智慧合約互動。 您可以使用乙太坊的 Azure 區塊鏈開發套件來取得合約 ABI。 您也可以從「密度編譯器」所建立的合約中繼資料檔案取得此檔案。

**若要使用開發工具組：**

如果您使用開發工具組或 Truffle 來建立您的智慧合約，您可以使用此延伸模組將合約 ABI 複製到剪貼簿。

1. 在 [Visual Studio Code explorer] 窗格中，展開您的密度專案的 [**組建/合約**] 資料夾。
1. 以滑鼠右鍵按一下 [合約中繼資料 JSON 檔案]。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 選取 [**複製合約 ABI**]。

    ![含有複製合約 ABI 選擇的 Visual Studio Code 窗格](./media/ethereum-logic-app/abi-devkit.png)

    合約 ABI 會複製到剪貼簿。

**若要使用合約中繼資料檔案：**

1. 開啟包含在您的密度專案的 [**組建/合約**] 資料夾中的合約中繼資料檔案。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 尋找 JSON 檔案中的**abi**區段。
1. 複製**abi** JSON 陣列。

    ![合約中繼資料檔案中的 ABI 程式碼](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>取得合約位元組碼

合約位元組程式碼是由乙太坊虛擬機器所執行的已編譯智慧合約。 您可以使用乙太坊的 Azure 區塊鏈開發套件來取得合約的位元組程式碼。 您也可以從「密度編譯器」取得它。

**若要使用開發工具組：**

如果您使用開發工具組或 Truffle 來建立您的智慧合約，您可以使用此延伸模組將合約位元組程式碼複製到剪貼簿。

1. 在 [Visual Studio Code explorer] 窗格中，展開您的密度專案的 [**組建/合約**] 資料夾。
1. 以滑鼠右鍵按一下 [合約中繼資料 JSON 檔案]。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 選取 [**複製合約位元組碼**]。

    ![具有複製合約位元組的選取範圍的 Visual Studio Code 窗格](./media/ethereum-logic-app/bytecode-devkit.png)

    合約位元組的會複製到剪貼簿。

**若要使用合約中繼資料檔案：**

1. 開啟包含在您的密度專案的 [**組建/合約**] 資料夾中的合約中繼資料檔案。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 尋找 JSON 檔案中的**位元組**檔元素。
1. 複製 [**位元組碼**] 值。

    ![在中繼資料中具有位元組的 Visual Studio Code 窗格](./media/ethereum-logic-app/bytecode-metadata.png)

**若要使用密度編譯器：**

使用命令 `solc --bin <smart contract>.sol` 來產生合約位元組碼。

## <a name="get-the-contract-address"></a>取得合約位址

合約位址是乙太坊區塊鏈上的智慧合約目的地位址。 您可以使用此位址來傳送智慧合約的交易或查詢狀態。 您可以從 Truffle 遷移輸出或合約中繼資料檔案取得合約位址。

**若要使用 Truffle 遷移輸出：**

Truffle 會在智慧合約部署之後顯示合約位址。 從輸出複製**合約位址**。

![在 Visual Studio Code 中使用合約位址 Truffle 遷移輸出](./media/ethereum-logic-app/contract-address-truffle.png)

**若要使用合約中繼資料檔案：**

1. 開啟包含在您的密度專案的 [**組建/合約**] 資料夾中的合約中繼資料檔案。 檔案名是智慧合約名稱，後面接著**json**延伸模組。
1. 尋找 JSON 檔案中的 [**網路**] 區段。
1. 私人網路是以整數網路識別碼來識別。 尋找 [網路] 區段內的 [位址] 值。
1. 複製**位址**值。

![位址值在 Visual Studio Code 中的中繼資料](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>後續步驟

觀看影片中的常見案例[使用 Logic Apps 執行更多](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true)作業。
