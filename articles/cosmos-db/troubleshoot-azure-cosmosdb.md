---
title: 在 Azure 儲存體總管中管理 Azure Cosmos DB
description: 學習如何在 Azure 儲存體總管中管理 Azure Cosmos DB。
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>儲存體總管中的 Azure Cosmos DB 疑難排解指南概觀

[Azure 儲存體總管中的 Azure Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) 是獨立應用程式，可讓您從 Windows、macOS 或 Linux 連線到裝載在 Azure 和主權雲端上的 Azure Cosmos DB 帳戶。 也可讓您管理 Azure Cosmos DB 實體、操縱資料、更新預存程序及觸發程序，以及其他 Azure 實體 (例如儲存體 Blob 及佇列)。

本指南摘要說明儲存體總管中有關 Azure Cosmos DB 的常見問題解決方案。

- [登入問題](#Sign-in-issues)
  - [信任鏈結中的自我簽署憑證](#Self-signed-certificate-in-certificate-chain)
  - [無法擷取訂用帳戶](#Unable-to-retrieve-subscriptions)
  - [看不到驗證頁面](#Unable-to-see-the-authentication-page)
  - [無法移除帳戶](#Cannot-remove-account)
- [Http/Https Proxy 的問題](#Http/Https-proxy-issue)
- [「本機與已連結」節點下的「開發」節點問題](#Development-node-under-Local-and-Attached-node-issue)
- [在「本機與已連結」節點中連結 Azure Cosmos DB 帳戶的錯誤](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [展開 Azure Cosmos DB 節點錯誤](#Expand-Azure-Cosmos-DB-node-error)
- [後續步驟](#Next-steps)

<h2 id="Sign-in-issues">登入問題</h2>

在繼續之前，請先嘗試重新啟動您的應用程式，查看是否能修正問題。

<h2 id="Self-signed-certificate-in-certificate-chain">信任鏈結中的自我簽署憑證</h2>

有幾個原因可能會導致此錯誤，最常見的兩個原因是：

1. 您在「透明 Proxy」背景，這表示有人 (例如您的 IT 部門) 是使用自我簽署憑證攔截 HTTPS 流量、解密再加密。

2. 您正在執行的軟體，例如防毒軟體，會將自我簽署的 SSL 憑證插入您收到的 HTTPS 訊息中。

當儲存體總管遇到這些「自我簽署憑證」的其中一個時，它可能就無法知曉收到的 HTTPS 訊息是否已遭竄改。 如果您有一份自我簽署憑證，則可以告知儲存體總管信任該憑證。 如果您不確定插入憑證的是誰，可以嘗試執行下列步驟來自行尋找：

1. 安裝 Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版即可)
     - Mac 和 Linux：應該包含在作業系統中
2. 執行 Open SSL
    - Windows：移至安裝目錄並找到 **/bin/**，然後按兩下 **openssl.exe**。
    - Mac 和 Linux：從終端機執行 **openssl**
3. 執行 `s_client -showcerts -connect microsoft.com:443`
4. 尋找自我簽署憑證。 如果不確定哪些是自我簽署的憑證，請尋找主旨 ("s:") 和簽發者 ("i:") 相同的所有位置。
5.  一旦發現任何自我簽署的憑證，請針對每個憑證，將從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。
6.  開啟儲存體總管，然後移至 [編輯] > [SSL 憑證] > [匯入憑證]。 使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果使用上述步驟找不到任何自我簽署的憑證，請傳送意見反應給我們，以取得更多協助。

<h2 id="Unable-to-retrieve-subscriptions">無法擷取訂用帳戶</h2>

如果您成功登入之後，卻無法擷取訂用帳戶：

- 透過登入 [Azure 入口網站](http://portal.azure.com/)確認您的帳戶可存取訂用帳戶的項目
- 確定已使用正確的環境登入 ([Azure](http://portal.azure.com/)、[Azure 中國](https://portal.azure.cn/)、[Azure 德國](https://portal.microsoftazure.de/)、[Azure 美國政府](http://portal.azure.us/)或自訂環境/Azure Stack)
- 如果您是在 Proxy 背景，請確定已正確設定儲存體總管的 Proxy
- 嘗試移除再重新新增帳戶
- 嘗試從主目錄 (例如：C:\Users\ContosoUser) 刪除下列檔案，再重新新增帳戶：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 登入時若出現任何錯誤訊息，請查看開發人員工具主控台 (f12)

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">看不到驗證頁面</h2>  

如果您看不到驗證頁面：

- 載入登入頁面可能需要一些時間，視連線速度而定，請等待至少一分鐘再關閉 [驗證] 對話方塊
- 如果您是在 Proxy 背景，請確定已正確設定儲存體總管的 Proxy
- 按下 F12 鍵，開發人員主控台會隨即顯示。 查看開發人員主控台的回應，看看能否找到任何驗證無法運作的線索

<h2 id="Cannot-remove-account">無法移除帳戶</h2>

如果無法移除帳戶，或重新驗證連結不執行任何動作

- 嘗試從主目錄刪除下列檔案，再重新新增帳戶：
  - .adalcache
  - .devaccounts
  - .extaccounts
- 如果您想要移除 SAS 連結的儲存體資源，請刪除：
  - Windows 是 %AppData%/StorageExplorer 資料夾
  - Mac 是 /Users/<您的名稱>/Library/Applicaiton SUpport/StorageExplorer
  - Linux 是 ~/.config/StorageExplorer
  - 如果刪除這些檔案，**您必須重新輸入所有認證**


<h2 id="Http/Https-proxy-issue">Http/Https Proxy 的問題</h2>

在 ASE 中設定 Http/Https Proxy 時，無法在左側樹狀目錄中列出 Azure Cosmos DB 節點。 這是已知問題，並將在下一個版本中修正。 目前您可以使用 Azure 入口網站中的 Azure Cosmos DB 資料總管解決此問題。 

<h2 id="Development-node-under-Local-and-Attached-node-issue">「本機與已連結」節點下的「開發」節點問題</h2>

當您在左側樹狀目錄中，按一下 [本機及連結] 節點下的 [開發] 節點後，系統沒有回應。  這是預期中的行為。 將在下一個版本中提供 Azure DB Cosmos 本機模擬器支援。

![開發節點](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">在「本機與已連結」節點中連結 Azure Cosmos DB 帳戶的錯誤</h2>

如果您在「本機與已連結」節點中連結 Azure Cosmos DB 帳戶後看到以下錯誤，請檢查您是否使用正確的連接字串。

![在本機與已連結中連結 Azure Cosmos DB 的錯誤](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">展開 Azure Cosmos DB 節點錯誤</h2>

當您嘗試展開左側的樹狀節點時，可能會看到下列錯誤。 

![展開錯誤](./media/troubleshoot-cosmosdb/expand-error.png)

請嘗試下列建議：

- 檢查 Azure Cosmos DB 帳戶是否正在進行佈建，並在帳戶成功建立後再試一次。
- 如果帳戶是在「快速存取」節點或「本機與已連結」節點下，請檢查帳戶是否已遭到刪除。 如果是這樣，您需要以手動方式移除節點。

<h2 id="Next-steps">後續步驟</h2>

如果沒有適合您的解決方案，請將問題詳細資料以電子郵件傳送給 Azure Cosmos DB 開發工具小組 ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com))，以修正問題。





