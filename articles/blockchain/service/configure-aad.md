---
title: 如何設定 Azure Active Directory 存取
description: 如何設定 Azure 區塊鏈 Service 與 Azure Active Directory 存取
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028211"
---
# <a name="how-to-configure-azure-active-directory-access"></a>如何設定 Azure Active Directory 存取

在本文中，您已了解如何授與存取權，並連接到使用 Azure Active Directory (Azure AD) 使用者、 群組或應用程式識別碼的 Azure 區塊鏈 Service 節點。

Azure AD 提供雲端式身分識別管理，並可讓您跨整個企業及存取的應用程式在 Azure 中使用單一身分識別。 Azure 區塊鏈服務與 Azure AD 整合，並提供的優點，例如識別碼同盟單一登入和多重要素驗證。

## <a name="prerequisites"></a>必要條件

* [建立使用 Azure 入口網站的區塊鏈成員](create-member.md)

## <a name="grant-access"></a>授與存取權

您可以授與成員層級和節點層級的存取。 在成員層級的存取權限授與時，並接著會授與存取權之成員底下的所有節點。

### <a name="grant-member-level-access"></a>授與成員層級存取權

若要授與成員層級的存取權限。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至**存取控制 (IAM) > 新增 > 新增角色指派**。
1. 選取 **區塊鏈成員節點存取 （預覽）** 角色並新增您想要授與存取權的 Azure AD 識別碼物件。 Azure AD 識別碼物件可以是：

    | Azure AD 物件 | 範例 |
    |-----------------|---------|
    | Azure AD 使用者   | `frank@contoso.onmicrosoft.com` |
    | Azure AD 群組  | `sales@contoso.onmicrosoft.com` |
    | 應用程式識別碼  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![新增角色指派](./media/configure-aad/add-role-assignment.png)

1. 選取 [ **儲存**]。

### <a name="grant-node-level-access"></a>授與節點層級存取權

1. 您可以授與在節點層級的存取權，方法是瀏覽對節點安全性，並按一下您想要授與存取權的節點名稱。
1. 選取的區塊鏈成員節點存取 （預覽） 角色並新增您想要授與存取權的 Azure AD 識別碼物件。 

## <a name="connect-using-azure-blockchain-connector"></a>使用 Azure 區塊鏈連接器進行連線

下載或複製[從 GitHub 的 Azure 區塊鏈連接器](https://github.com/Microsoft/azure-blockchain-connector/)。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

本快速入門一節中的後續**讀我檔案**建置的連接器，從原始程式檔。

### <a name="connect-using-an-azure-ad-user-account"></a>使用 Azure AD 使用者帳戶連線

1. 執行下列命令，以使用 Azure AD 使用者帳戶進行驗證。 取代\<myAADDirectory\>與 Azure AD 網域。 例如： `yourdomain.onmicrosoft.com`。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD 會提示您輸入認證。
1. 使用您的使用者名稱和密碼登入。
1. 驗證成功後，您的本機 proxy 連線到您的區塊鏈節點。 您現在可以附加 Geth 用戶端與本機端點。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>連接使用的應用程式識別碼

許多應用程式向 Azure AD 中使用應用程式識別碼，而不 Azure AD 使用者帳戶。

若要使用連線到您節點應用程式識別碼，取代**aadauthcode**具有**aadclient**。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| 參數 | 描述 |
|-----------|-------------|
| tenant-id | Azure AD 網域，例如， `yourdomain.onmicrosoft.com`
| client-id | 在 Azure AD 中註冊的應用程式的用戶端識別碼
| client-secret | 在 Azure AD 中註冊的應用程式的用戶端祕密

如需有關如何在 Azure AD 中註冊應用程式的詳細資訊，請參閱[How to:使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>連接行動裝置或文字瀏覽器

對於行動裝置或以文字為基礎的 Azure AD 驗證的快顯顯示不可能的瀏覽器中，Azure AD 會產生一次性密碼。 您可以複製密碼，並繼續使用另一個環境中的 Azure AD 驗證。

若要產生密碼，取代**aadauthcode**具有**aaddevice**。 取代\<myAADDirectory\>與 Azure AD 網域。 例如： `yourdomain.onmicrosoft.com`。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>後續步驟

如需有關 Azure 區塊鏈 Service 中的資料安全性的詳細資訊，請參閱：

> [!div class="nextstepaction"]
> [Azure 區塊鏈服務安全性](data-security.md)