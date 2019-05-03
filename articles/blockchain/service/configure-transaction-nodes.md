---
title: 設定 Azure 區塊鏈 Service 交易節點
description: 如何設定 Azure 區塊鏈 Service 交易節點
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dffeb81ae1eb244c38639a1241c0581e6fcdf94a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027956"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>設定 Azure 區塊鏈 Service 交易節點

與 Azure 區塊鏈服務互動，則透過連線到區塊鏈成員中的一或多個交易節點。  為了與交易節點互動，您必須設定您的節點進行存取。

## <a name="prerequisites"></a>必要條件

* [建立 Azure 區塊鏈成員](create-member.md)

## <a name="transaction-node-overview"></a>交易節點概觀

異動節點都會用來將區塊鏈交易傳送至 Azure 區塊鏈服務中，透過公用端點。 預設交易節點包含已註冊的區塊鏈，Ethereum 帳戶的私密金鑰，因此無法刪除。

若要檢視的預設交易節點詳細資料：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至您的 Azure 區塊鏈服務成員。 選取 **交易節點**。

    ![選取的預設交易節點](./media/configure-transaction-nodes/nodes.png)

    概觀詳細資料包括公開的端點位址和公用金鑰。

## <a name="create-transaction-node"></a>建立交易節點

您可以將最多 9 個額外的交易節點加入您的區塊鏈成員，總共 10 個交易的節點。 藉由新增交易節點，您可以提高延展性，或分散負載。 例如，您可能有不同的用戶端應用程式的交易節點端點。

若要新增交易節點：

1. 在 Azure 入口網站中，瀏覽至您的 Azure 區塊鏈服務成員，然後選取**交易節點 > 新增**。
1. 完成新的 [交易] 節點的設定。

    ![新增交易節點](./media/configure-transaction-nodes/add-node.png)

    | 設定 | 描述 |
    |---------|-------------|
    | 名稱 | 異動節點名稱。 名稱用來建立交易節點端點的 DNS 位址。 例如： `newnode-myblockchainmember.blockchain.azure.com`。 建立後即無法變更節點名稱。 |
    | 密碼 | 設定強式密碼。 您可以使用密碼來存取使用基本驗證的交易節點端點。

1. 選取 [建立] 。

    佈建一個新的交易節點需要約 10 分鐘。 其他交易節點會產生費用。 如需有關成本的詳細資訊，請參閱[Azure 定價](https://aka.ms/ABSPricing)。

## <a name="endpoints"></a>端點

交易的節點具有唯一的 DNS 名稱和公用端點。

若要檢視交易 節點的端點詳細資料：

1. 在 Azure 入口網站中，瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**概觀**。

    ![端點](./media/configure-transaction-nodes/endpoints.png)

異動節點端點安全，而且需要驗證。 您可以連接至使用 Azure AD 驗證，HTTPS 基本驗證，並使用透過 HTTPS 或 Websocket 的存取金鑰，透過 SSL 交易端點。

### <a name="azure-active-directory-access-control"></a>Azure Active Directory 存取控制

Azure 區塊鏈服務交易節點端點支援 Azure Active Directory (Azure AD) 驗證。 您可以授與 Azure AD 使用者、 群組和服務主體存取您的端點。

若要授與 Azure AD 存取控制您的端點：

1. 在 Azure 入口網站中，瀏覽至您的 Azure 區塊鏈服務成員，然後選取**交易節點 > 存取控制 (IAM) > 新增 > 新增角色指派**。
1. 建立新的角色指派的使用者、 群組或服務主體 （應用程式角色）。

    ![新增 IAM 角色](./media/configure-transaction-nodes/add-role.png)

    | 設定 |  動作 |
    |---------|-------------|
    | 角色 | 選取 **擁有者**，**參與者**，或**讀取器**。
    | 存取權指派對象 | 選取  **Azure AD 使用者、 群組或服務主體**。
    | 选择 | 搜尋使用者、 群組或您想要新增的服務主體。

1. 選取 **儲存**新增角色指派。

如需有關 Azure AD 存取控制的詳細資訊，請參閱[管理使用 RBAC 和 Azure 入口網站的 Azure 資源的存取權](../../role-based-access-control/role-assignments-portal.md)

如需有關如何使用 Azure AD 驗證進行連接的詳細資訊，請參閱 <<c0> [ 連接到您使用 AAD 驗證的節點](configure-aad.md)。

### <a name="basic-authentication"></a>基本驗證

用於 HTTPS 基本驗證，使用者名稱和密碼認證會在 HTTPS 要求的標頭中傳遞至端點。

您可以在 Azure 入口網站中檢視交易節點的基本驗證端點詳細資料。 瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**基本驗證**設定中。

![基本驗證](./media/configure-transaction-nodes/basic.png)

使用者名稱會為您的節點名稱，而無法變更。

若要使用的 URL，取代\<密碼\>時已佈建節點設定的密碼。 您可以藉由選取更新的密碼**重設密碼**。

### <a name="access-keys"></a>存取金鑰

針對存取驗證、 存取金鑰包含在端點 URL。 佈建 [異動] 節點時，會產生兩個存取金鑰。 這兩個存取金鑰可用於驗證。 兩個金鑰可讓您變更和輪替金鑰。

您可以檢視交易節點的存取金鑰的詳細資料，並複製包含的存取金鑰的端點位址。 瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**便捷鍵**設定中。

### <a name="firewall-rules"></a>防火牆規則

防火牆規則可讓您限制可以嘗試驗證您的交易節點的 IP 位址。  如果交易節點未設定防火牆規則，它無法存取任何合作對象。  

若要檢視交易 節點的防火牆規則，瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**防火牆規則**設定中。

您可以新增防火牆規則的輸入規則名稱、 起始 IP 位址和結束 IP 位址在中**防火牆規則**方格。

![防火牆規則](./media/configure-transaction-nodes/firewall-rules.png)

若要啟用：

* **單一 IP 位址：** 設定開始和結束 IP 位址的相同 IP 位址。
* **IP 位址範圍：** 設定開始和結束 IP 位址範圍。 比方說，範圍在 10.221.34.0 開始與結束 10.221.34.255，可讓整個 10.221.34.xxx 子網路。
* **允許所有 IP 位址：** 設定起始 IP 位址為 0.0.0.0 和 255.255.255.255 結束 IP 位址。

## <a name="connection-strings"></a>連接字串

異動節點的連接字串語法供 basic 驗證，或使用存取金鑰。 提供透過 HTTPS 和 WebSockets 包括存取索引鍵的連接字串。

您可以檢視交易 節點的連接字串，並複製端點位址。 瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**連接字串**設定中。

![連接字串](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>範例程式碼

範例程式碼可快速啟用 連線到透過 Web3、 Nethereum、 Web3js 和 Truffle 您交易的節點。

您可以檢視交易 節點的範例連接程式碼，並將它複製到使用熱門的開發人員工具使用。 瀏覽至您的 Azure 區塊鏈服務成員交易節點的其中一個，然後選取**範例程式碼**設定中。

選擇 [Web3 或 Nethereum] 索引標籤來檢視您想要使用的程式碼範例。

![範例程式碼](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定使用 Azure CLI 的交易節點](manage-cli.md)
