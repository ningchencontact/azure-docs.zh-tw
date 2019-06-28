---
title: 連線到 SFTP 伺服器與 SSH-Azure Logic Apps
description: 藉由使用 SSH 和 Azure Logic Apps，讓針對 SFTP 伺服器監視、建立、管理、傳送及接收檔案的工作自動化
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 7479be6a14c7d1ace5d60defad0eda51d2aa814b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296572"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>藉由使用 SSH 和 Azure Logic Apps 來監視、建立及管理 SFTP 檔案

若要使用 [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) 通訊協定在[安全檔案傳輸通訊協定 (SFTP)](https://www.ssh.com/ssh/sftp/) \(英文\) 伺服器上，將監視、建立、傳送及接收檔案的工作自動化，您可以使用 Azure Logic Apps 和 SFTP-SSH 連接器來建置整合工作流程並自動化。 SFTP 是一個網路通訊協定，可透過任何可靠的資料流提供檔案存取、檔案傳輸和檔案管理。 以下是一些您可自動化的範例工作：

* 監視檔案何時新增或變更。
* 取得、建立、複製、重新命名、更新、列出及刪除檔案。
* 建立資料夾。
* 取得檔案內容與中繼資料。
* 將封存檔案解壓縮到資料夾。

您可以使用觸發程序來監視 SFTP 伺服器上的事件，並讓輸出可供其他動作使用。 您可以使用動作，在 SFTP 伺服器上執行各種工作。 您也可以讓邏輯應用程式中的其他動作使用 SFTP 動作的輸出。 例如，如果您定期從 SFTP 伺服器擷取檔案，可以藉由使用 Office 365 Outlook 連接器或 Outlook.com 連接器，傳送關於那些檔案及其內容的電子郵件警示。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)

SFTP-SSH 連接器與 SFTP 連接器之間的差異，請檢閱[比較 SFTP-SSH 和 SFTP](#comparison)本主題稍後的章節。

## <a name="limits"></a>限制

* 根據預設，SFTP-SSH 動作才能讀取或寫入的檔案*1GB 或更小*，但只有*15MB*區塊一次。 若要處理檔案大於 15 MB，SFTP-SSH 動作支援[訊息區塊處理](../logic-apps/logic-apps-handle-large-messages.md)，除了所複製的檔案動作，此運算子可以處理 15MB 的檔案。 **取得檔案內容**動作會隱含地使用區塊處理訊息。 

* SFTP-SSH 觸發程序不支援區塊處理。 當要求檔案的內容時，觸發程序選取 是 15 MB 的檔案或更小。 若要取得大於 15 MB 的檔案，請改為遵循此模式：

  * 使用 SFTP-SSH 觸發程序會傳回檔案內容，例如**檔案新增或修改 （僅限屬性） 的時**。

  * 請依照下列觸發程序與 SFTP-SSH**取得檔案內容**讀取完整的檔案，並會隱含地使用區塊處理訊息的動作。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>比較 SFTP-SSH 與 SFTP

以下是 SFTP-SSH 連接器與 SFTP 連接器之間的其他主要差異，其中 SFTP-SSH 連接器具備這些功能：

* 會使用[SSH.NET 程式庫](https://github.com/sshnet/SSH.NET)，這是支援.NET 開放原始碼安全殼層 (SSH) 程式庫。

  > [!NOTE]
  >
  > SFTP-SSH 連接器*只*支援這些私密金鑰、格式、演算法和指紋：
  >
  > * **私密金鑰格式**：OpenSSH 和 ssh.com 格式的 RSA (Rivest Shamir Adleman) 與 DSA (Digital Signature Algorithm) 金鑰
  > * **加密演算法**：DES-EDE3-CBC、DES-EDE3-CFB、 DES-CBC、AES-128-CBC、AES-192-CBC 和 AES-256-CBC
  > * **指紋**：MD5

* 根據預設，SFTP-SSH 動作才能讀取或寫入的檔案*1GB 或更小*，但只有*15MB*區塊一次。 若要處理檔案大於 15 MB，可以使用 SFTP-SSH 動作[訊息區塊處理](../logic-apps/logic-apps-handle-large-messages.md)。 不過，所複製的檔案動作支援只有 15 MB 的檔案，因為該動作不支援區塊處理訊息。 SFTP-SSH 觸發程序不支援區塊處理。

* 提供**建立資料夾**動作，可在 SFTP 伺服器上指定的路徑中建立資料夾。

* 提供**重新命名檔案**動作，可重新命名 SFTP 伺服器上的檔案。

* 可將連線快取至 SFTP 伺服器*最多 1 小時*，這可以改善效能並減少嘗試連線伺服器的次數。 若要設定此快取行為的持續期間，請編輯 SFTP 伺服器 SSH 組態中的 [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) 屬性。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您的 SFTP 伺服器位址和帳戶認證，這讓您的邏輯應用程式能夠存取您的 SFTP 帳戶。 您也可以存取 SSH 私密金鑰和 SSH 私密金鑰密碼。

  > [!IMPORTANT]
  >
  > SFTP-SSH 連接器*只*支援這些私密金鑰格式、演算法和指紋：
  >
  > * **私密金鑰格式**：OpenSSH 和 ssh.com 格式的 RSA (Rivest Shamir Adleman) 與 DSA (Digital Signature Algorithm) 金鑰
  > * **加密演算法**：DES-EDE3-CBC、DES-EDE3-CFB、 DES-CBC、AES-128-CBC、AES-192-CBC 和 AES-256-CBC
  > * **指紋**：MD5
  >
  > 當您建立邏輯應用程式時，必須在新增所需的 SFTP-SSH 觸發程序或動作之後，提供適用於 SFTP 伺服器的連線資訊。 
  > 如果您使用 SSH 私密金鑰，請確定會從您的 SSH 私密金鑰檔案「複製」金鑰，然後將該金鑰「貼到」連線詳細資料中，「請勿手動輸入或編輯該金鑰」，這樣可能導致連線失敗。 
  > 如需詳細資訊，請參閱本文稍後的步驟。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 您要在其中存取 SFTP 帳戶的邏輯應用程式。 若要開始使用 SFTP-SSH 觸發程序，請[建立空白邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP-SSH 動作，請使用其他觸發程序來啟動邏輯應用程式，例如「週期」  觸發程序。

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH 觸發程序的運作方式

SFTP-SSH 觸發程序的運作方式輪詢 SFTP 檔案系統，並尋找任何自上次輪詢後已變更的檔案。 某些工具可讓您在檔案變更時保留時間戳記。 在這些情況下，您必須停用此功能，以便讓您的觸發程序可以運作。 以下是一些常見的設定：

| SFTP 用戶端 | 動作 |
|-------------|--------|
| Winscp | 移至 [選項]   > [喜好設定]   > [傳輸]   > [編輯]   > [保留時間戳記]   > [停用]  |
| FileZilla | 移至 [傳輸]   > [保留傳輸檔案的時間戳記]   > [停用]  |
|||

當觸發程序找到新檔案時，觸發程序會確認該新檔案是完整檔案，而不是部分寫入的檔案。 例如，當觸發程序檢查檔案伺服器時，檔案可能正在進行變更。 為避免傳回部分寫入的檔案，觸發程序會備註最近發生變更之檔案的時間戳記，但不會立即傳回該檔案。 觸發程序只有在再次輪詢伺服器時，才會傳回該檔案。 有時，此行為可能會導致最長可達觸發程序輪詢間隔兩倍的延遲。

## <a name="connect-to-sftp-with-ssh"></a>使用 SSH 連線至 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請在搜尋方塊中輸入 "sftp ssh" 作為篩選條件。 在觸發程序清單底下，選取您想要的觸發程序。

   -或-

   若是現有的邏輯應用程式，請在想要新增動作的最後一個步驟底下，選擇 [新增步驟]  。 在搜尋方塊中，輸入 "sftp ssh" 作為篩選條件。 在動作清單底下，選取您想要的動作。

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 選擇顯示的加號 ( **+** )，然後選取 [新增動作]  。

1. 為您的連線提供必要的詳細資料。

   > [!IMPORTANT]
   >
   > 當您在 [SSH 私密金鑰]  屬性中輸入 SSH 私密金鑰之後，請執行這些額外的步驟，以協助確定您會為這個屬性提供完整且正確的值。 
   > 無效的金鑰會導致連線失敗。

   雖然您可以使用任何文字編輯器，但下列範例步驟會使用 Notepad.exe 作為範例，來示範如何正確地複製並貼上您的金鑰。

   1. 在文字編輯器中開啟您的 SSH 私密金鑰檔案。 這些步驟會使用記事本作為範例。

   1. 在 [記事本]**編輯**功能表上，選取**全選**。

   1. 選取 [編輯]   > [複製]  。

   1. 在您新增的 SFTP-SSH 觸發程序或動作中，將所複製的「完整」  金鑰貼到 [SSH 私密金鑰]  屬性中，此屬性支援多行。  ***請確定您會貼上***金鑰。 ***請勿手動輸入或編輯此金鑰***。

1. 當您完成輸入連線詳細資料之後，請選擇 [建立]  。

1. 現在請為您選取的觸發程序或動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="examples"></a>範例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH 觸發：當新增或修改檔案時

此觸發程序會在 SFTP 伺服器上新增或變更了檔案時，啟動邏輯應用程式工作流程。 舉例來說，您可以新增條件來檢查檔案的內容，並根據內容是否符合指定條件來取得內容。 接著，您可以新增要取得檔案內容的動作，並將該內容放置於 SFTP 伺服器上的資料夾中。

**企業範例**：您可以使用此觸發程序，來監視代表客戶訂單的新檔案 SFTP 資料夾。 然後，您可以使用 SFTP 動作 (例如**取得檔案內容**)，來取得訂單的內容以進一步處理，並將該訂單儲存在訂單資料庫中。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH 動作：使用路徑來取得內容

此動作會從 SFTP 伺服器上的檔案取得內容。 舉例來說，您可以新增來自上一個範例中的觸發程序，以及新增檔案內容必須符合的條件。 如果條件為 true，則可以執行會取得內容的動作。

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](/connectors/sftpconnector/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
