---
title: Azure 區塊鏈服務安全性
description: Azure 區塊鏈服務資料的存取和安全性概念
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028196"
---
# <a name="azure-blockchain-service-security"></a>Azure 區塊鏈服務安全性

Azure 區塊鏈服務會使用數個 Azure 功能，讓資料保持安全且可用。 使用隔離、 加密及驗證來保護資料。

## <a name="isolation"></a>隔離

Azure 區塊鏈服務資源被隔離的私人虛擬網路中。 每個交易和驗證的節點是虛擬機器 (VM)。 一個虛擬網路中的 Vm 無法直接與不同的虛擬網路中的 Vm 通訊。 隔離能確保通訊仍然隱蔽於虛擬網路內。 如需有關 Azure 虛擬網路隔離的詳細資訊，請參閱 < [Azure 公用雲端中的隔離](../../security/azure-isolation.md#networking-isolation)。

![VNET 圖表](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

使用者資料會儲存在 Azure 儲存體。 使用者資料是動態及待用加密的安全性與機密性。 如需詳細資訊，請參閱[Azure 儲存體安全性指南](../../storage/common/storage-security-guide.md)。

## <a name="authentication"></a>Authentication

交易可以傳送到區塊鏈節點透過 RPC 端點。 用戶端通訊使用反向 proxy 伺服器的交易節點會處理使用者驗證和透過 SSL 加密資料。

![驗證圖表](./media/data-security/authentication.png)

有三種 RPC 存取的驗證模式。

### <a name="basic-authentication"></a>基本驗證

基本驗證會使用 HTTP 驗證標頭包含的使用者名稱和密碼。 使用者名稱是區塊鏈節點的名稱。 在成員或節點的佈建期間設定密碼。 可以使用 Azure 入口網站或 CLI 變更的密碼。

### <a name="access-keys"></a>存取金鑰

存取金鑰會使用隨機產生的字串，包含在端點 URL。 兩個存取金鑰可協助啟用金鑰輪替。 可以重新產生金鑰，以及從 Azure 入口網站和 CLI。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) 使用宣告架構驗證機制使用 Azure AD 使用者認證的 Azure ad 驗證使用者的位置。 Azure AD 提供雲端式身分識別管理，並可讓客戶在雲端上整個企業及存取應用程式間使用單一身分識別。 Azure 區塊鏈服務會與啟用識別碼同盟單一登入和多重要素驗證的 Azure AD 整合。 您可以存取的區塊鏈成員和節點組織中指派的使用者、 群組和應用程式角色。

Azure AD 用戶端 proxy 位於[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)。 用戶端 proxy 會使用者導向至 Azure AD 登入頁面，並取得驗證成功後的持有人權杖。 接下來，使用者會以太坊用戶端應用程式，例如 Geth 或 Truffle 連線至用戶端 proxy 的端點。 最後，當提交異動時，用戶端 proxy 會插入在 http 標頭的持有人權杖，並反向 proxy 會驗證權杖使用 OAuth 通訊協定。

## <a name="keys-and-ethereum-accounts"></a>索引鍵和以太坊帳戶

當佈建的 Azure 區塊鏈服務的成員，就會產生以太坊帳戶和公用和私用金鑰組。 私密金鑰用來傳送到區塊鏈的交易。 以太坊帳戶是公用金鑰的雜湊的最後的 20 個位元組。 以太坊帳戶也稱為 「 電子錢包 」。

私用和公用金鑰組會儲存為 JSON 格式的金鑰檔。 使用建立區塊鏈分類帳服務時所輸入的密碼，私用的金鑰進行加密。

私密金鑰用來數位簽署的交易。 在私用區塊鏈，由私密金鑰所簽署的智慧合約表示簽署者的身分識別。 若要確認簽章的有效性，接收者可以比較簽署者的公開金鑰計算而得的簽章的位址。

Constellation 金鑰用來唯一識別的仲裁節點。 Constellation 金鑰會在節點佈建期間產生，並在私用的交易，仲裁 privateFor 參數中指定。

## <a name="next-steps"></a>後續步驟

[設定 Azure 區塊鏈 Service 交易節點](configure-transaction-nodes.md)
