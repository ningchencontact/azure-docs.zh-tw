---
title: 部署 Azure Blockchain Workbench
description: 如何部署 Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/17/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: bcd08ac8563edfaf4297e26ad42ed8bc62d86918
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831630"
---
# <a name="deploy-azure-blockchain-workbench"></a>部署 Azure Blockchain Workbench

您可以使用 Azure Marketplace 中的解決方案範本來部署 Azure Blockchain Workbench。 該範本可讓您輕鬆部署建立區塊鏈應用程式所需的元件。 部署完成後，Blockchain Workbench 將可讓您存取用戶端應用程式，以建立及管理使用者和區塊鏈應用程式。

如需關於 Blockchain Workbench 元件的詳細資訊，請參閱 [Azure Blockchain Workbench 架構](blockchain-workbench-architecture.md)。

## <a name="prepare-for-deployment"></a>準備部署

Blockchain Workbench 可讓您部署區塊鏈總帳與一組相關的 Azure 服務，這些 Azure 服務最常用來建置以區塊鏈為基礎的應用程式。 部署 Blockchain Workbench 會使下列 Azure 服務佈建在您 Azure 訂用帳戶中的資源群組內。

* 1 個 Event Grid 主題
* 1 個服務匯流排命名空間
* 1 個Application Insights
* 1 個 SQL Database (標準 S0)
* 2 個應用程式服務 (標準)
* 2 個 Azure Key Vault
* 2 個 Azure 儲存體帳戶 (標準 LRS)
* 2 個虛擬機器擴展集 (用於驗證程式與背景工作節點)
* 2 個虛擬網路 (包括負載平衡器、網路安全性群組和每個虛擬網路的公用 IP 位址)
* 選擇性：Azure 監視器

以下是在 **myblockchain** 資源群組中建立的部署範例。

![部署範例](media/blockchain-workbench-deploy/example-deployment.png)

Blockchain Workbench 的成本是彙總基礎 Azure 服務的成本。 Azure 服務的定價資訊可使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來計算。

要部署 Azure Blockchain Workbench 必須符合幾項必要條件。 必要條件包括 Azure AD 設定和應用程式註冊。

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 應用程式註冊

要部署 Blockchain Workbench，必須要註冊 Azure AD 應用程式。 您必須要有 Azure Active Directory (Azure AD) 租用戶才能註冊應用程式。 您可以使用現有的租用戶，或建立新的租用戶。 如果您要使用現有的 Azure AD 租用戶，您必須要有足夠的權限才能在 Azure AD 租用戶中註冊應用程式。 應用程式註冊必須在部署 Workbench 之訂用帳戶的訂用帳戶管理員的租用戶中執行。 如需 Azure AD 租用戶的詳細資訊，請參閱[如何取得 Active Directory 租用戶](../active-directory/develop/active-directory-howto-tenant.md)和[整合應用程式與 Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在右上角選取帳戶，並切換至所需的 Azure AD 租用戶。 此租用戶應為部署 Workbench 之訂用帳戶的訂用帳戶管理員的租用戶，且您必須有足夠的權限可註冊應用程式。
3. 在左側導覽窗格中，選取 [Azure Active Directory] 服務。 選取 [應用程式註冊] > [新增應用程式註冊]。

    ![應用程式註冊](media/blockchain-workbench-deploy/app-registration.png)

4. 提供應用程式的 [名稱] 和 [登入 URL]。 您可以使用預留位置值，因為值在部署期間將會變更。 

    ![建立應用程式註冊](media/blockchain-workbench-deploy/app-registration-create.png)

    |設定  | 值  |
    |---------|---------|
    |Name | `Blockchain API` |
    |應用程式類型 |Web 應用程式/API|
    |登入 URL | `https://blockchainapi` |

5. 選取 [建立] 以註冊 Azure AD 應用程式。

### <a name="modify-application-manifest"></a>修改應用程式資訊清單

接著，您必須修改應用程式資訊清單，以使用 Azure AD 中的應用程式角色來指定 Blockchain Workbench 管理員。  如需應用程式資訊清單的詳細資訊，請參閱 [Azure Active Directory 應用程式資訊清單](../active-directory/develop/active-directory-application-manifest.md)。

1. 針對您所登錄的應用程式，在 [已註冊的應用程式詳細資料] 窗格中選取 [資訊清單]。
2. 產生 GUID。 您可以使用 PowerShell 命令 [guid] :: NewGuid () 或 New-GUID Cmdlet 產生 GUID。 另一個選項是使用 GUID 產生器網站。
3. 您將會更新資訊清單的 [appRoles] 區段。 在 [編輯資訊清單] 窗格中，選取 [編輯]，並將 `"appRoles": []` 取代為提供的 JSON。 請務必將 [識別碼] 欄位的值取代為您所產生的 GUID。 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![編輯資訊清單](media/blockchain-workbench-deploy/edit-manifest.png)

    > [!IMPORTANT]
    > 您必須以 [管理員] 值識別 Blockchain Workbench 管理員。

4.  按一下 [儲存]，以儲存應用程式資訊清單的變更。

### <a name="add-graph-api-required-permissions"></a>新增圖形 API 的必要權限

API 應用程式必須向使用者要求存取目錄的權限。 請為 API 應用程式設定下列必要權限：

1. 在 Blockchain API 應用程式註冊中，選取 [設定] > [所需的權限] > [選取 API] > [Microsoft Graph]。

    ![選取 API](media/blockchain-workbench-deploy/client-app-select-api.png)

    按一下 [選取] 。

2. 在 [啟用存取] 中的 [應用程式權限] 下方，選擇 [讀取所有使用者的完整設定檔]。

    ![啟用存取](media/blockchain-workbench-deploy/client-app-read-perms.png)

    按一下 [選取]，然後按一下 [完成]。

3. 在 [所需的權限] 中選取 [授與權限]，然後在出現驗證提示時選取 [是]。

   ![授與權限](media/blockchain-workbench-deploy/client-app-grant-permissions.png)

   授與權限後，Blockchain Workbench 即可存取目錄中的使用者。 必須具有讀取權限，才能搜尋成員，以及將成員新增至 Blockchain Workbench。

### <a name="add-graph-api-key-to-application"></a>將圖形 API 金鑰新增至應用程式

Blockchain Workbench 會以 Azure AD 作為主要身分識別管理系統，供使用者與區塊鏈應用程式進行互動。 若要讓 Blockchain Workbench 能夠存取 Azure AD 和擷取使用者資訊 (例如名稱和電子郵件)，您必須新增存取金鑰。 Blockchain Workbench 會使用此金鑰對 Azure AD 進行驗證。

1. 針對您所登錄的應用程式，在 [已註冊的應用程式詳細資料] 窗格中選取 [設定]。
2. 選取 [金鑰]。
3. 藉由指定金鑰**說明**並選擇**有效期間**值，來新增金鑰。 

    ![建立金鑰](media/blockchain-workbench-deploy/app-key-create.png)

    |設定  | 值  |
    |---------|---------|
    | 說明 | `Service` |
    | Expires | 選擇有效期間 |

4. 選取 [ **儲存**]。 
5. 複製金鑰的值並加以儲存，以供日後使用。 您在部署時將會用到該值。

    > [!IMPORTANT]
    >  若未儲存金鑰供部署使用，您將必須產生新的金鑰。 您無法後續再從入口網站擷取金鑰值。

### <a name="get-application-id"></a>取得應用程式識別碼

部署時必須要有應用程式識別碼和租用戶資訊。 請收集並儲存這些資訊，以供部署期間使用。

1. 針對您所登錄的應用程式，選取 [設定] > [屬性]。
2.  在 [屬性] 窗格中複製並儲存下列值，以供後續於部署期間使用。

    ![API 應用程式屬性](media/blockchain-workbench-deploy/app-properties.png)

    | 設定以儲存  | 用於部署 |
    |------------------|-------------------|
    | 應用程式識別碼 | Azure Active Directory 設定 > 應用程式識別碼 |

### <a name="get-tenant-domain-name"></a>取得租用戶網域名稱

收集並儲存應用程式註冊所在的 Active Directory 租用戶網域名稱。 

在左側導覽窗格中，選取 [Azure Active Directory] 服務。 選取 [自訂網域名稱]。 複製並儲存網域名稱。

![網域名稱](media/blockchain-workbench-deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>部署 Blockchain Workbench

在必要條件步驟完成後，您即可部署 Blockchain Workbench。 以下幾節將概述如何部署架構。

1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在右上角選取帳戶，並切換至要部署 Azure Blockchain Workbench 的適當 Azure AD 租用戶。
3.  在左窗格中選取 [建立資源]。 在 [搜尋 Marketplace] 搜尋列中搜尋 `Azure Blockchain Workbench`。 

    ![Marketplace 搜尋列](media/blockchain-workbench-deploy/marketplace-search-bar.png)

4.  選取 [Azure Blockchain Workbench]。

    ![Marketplace 搜尋結果](media/blockchain-workbench-deploy/marketplace-search-results.png)

4.  選取 [建立] 。
5.  完成基本設定。

    ![建立 Azure Blockchain Workbench](media/blockchain-workbench-deploy/blockchain-workbench-settings-basic.png)

    | 設定 | 說明  |
    |---------|--------------|
    | 資源前置詞 | 部署的簡短唯一識別項。 此值會作為命名資源的基礎。 |
    | VM 使用者名稱 | 此使用者名稱會作為所有虛擬機器 (VM) 的管理員。 |
    | 驗證類型 | 選取您要使用密碼還是金鑰連線至 VM。 |
    | 密碼 | 此密碼會用來連線至 VM。 |
    | SSH | 使用開頭為 **ssh-rsa** 的單行格式 RSA 公開金鑰，或使用多行 PEM 格式。 您可以在 Linux 和 OS X 上使用 `ssh-keygen` 來產生 SSH 金鑰，或在 Windows 上使用 PuTTYGen 產生。 如需 SSH 金鑰的詳細資訊，請參閱[如何在 Azure 上對 Windows 使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)。 |
    | 資料庫密碼/確認資料庫密碼 | 指定要用來存取在部署過程中建立之資料庫的密碼。 |
    | 部署區域 | 指定 Blockchain Workbench 資源的部署位置。 為獲得最佳可用性，此位置應符合**位置**設定。 |
    | 訂用帳戶 | 指定要用於部署的 Azure 訂用帳戶。 |
    | 資源群組 | 選取 [新建] 以建立新的資源群組，並指定唯一的資源群組名稱。 |
    | 位置 | 指定要部署架構的區域。 |

6.  選取 [確定]，以完成基本設定組態區段。

7.  完成 [Azure Active Directory 設定]。

    ![Azure AD 設定](media/blockchain-workbench-deploy/blockchain-workbench-settings-aad.png)

    | 設定 | 說明  |
    |---------|--------------|
    | 網域名稱 | 使用在[取得租用戶網域名稱](#get-tenant-domain-name)必要條件一節中收集到的 Azure AD 租用戶。 |
    | 應用程式識別碼 | 使用在[取得應用程式識別碼](#get-application-id)必要條件一節中收集到的 Blockchain 用戶端應用程式註冊中的應用程式識別碼。 |
    | 應用程式金鑰 | 使用在[將圖形 API 金鑰新增至應用程式](#add-graph-api-key-to-application)必要條件一節中收集到的 Blockchain 用戶端應用程式註冊中的應用程式金鑰。 |


8.  按一下 [確定]，以完成 [Azure AD 參數組態] 區段。

9.  完成 [網路大小和效能] 設定。

    ![網路和效能設定](media/blockchain-workbench-deploy/blockchain-workbench-settings-network.png)

    | 設定 | 說明  |
    |---------|--------------|
    | 區塊鏈節點數目 | 選擇要在您的網路中部署的 Ethereum PoA 驗證程式節點數目。 |
    | 儲存體效能 | 選擇您的區塊鏈網路慣用的 VM 儲存體效能。 |
    | 虛擬機器大小 | 選擇您的區塊鏈網路慣用的 VM 大小。 |

10. 選取 [確定]，以完成 [網路大小和效能] 區段。

11. 完成 [Azure 監視器] 設定。

    ![Azure 監視器](media/blockchain-workbench-deploy/blockchain-workbench-settings-oms.png)

    | 設定 | 說明  |
    |---------|--------------|
    | 監視 | 選擇是否要讓 Azure 監視器監視區塊鏈網路 |
    | 連線至現有的 Log Analytics 執行個體 | 選擇您要使用現有的 Log Analytics 執行個體，還是要建立新的。 如果使用現有執行個體，請輸入工作區識別碼及主要金鑰。 |

12. 按一下 [確定] 以完成 [Azure 監視器] 區段。

13. 檢閱摘要，以確認您的參數正確無誤。

    ![總結](media/blockchain-workbench-deploy/blockchain-workbench-summary.png)

14. 選取 [建立]，以同意條款並部署您的 Azure Blockchain Workbench。

部署最多需要 90 分鐘的時間。 您可以使用 Azure 入口網站來監視進度。 在新建立的資源群組中選取 [部署] > [概觀]，以查看已部署的構件狀態。

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench Web URL

Blockchain Workbench 部署完成後，新的資源群組即會包含您的 Blockchain Workbench 資源。 Blockchain Workbench 服務可透過 Web URL 來存取。 下列步驟說明如何擷取已部署之架構的 Web URL。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側導覽窗格中，選取 [資源群組]
3. 選擇您在部署 Blockchain Workbench 時指定的資源群組名稱。
4. 按一下 [類型] 資料行標題，依類型按字母順序來排序清單。
5. 有兩項屬於 **App Service** 類型的資源。 選取屬於 **App Service** 類型、且*沒有* "-api" 尾碼的資源。

    ![應用程式服務清單](media/blockchain-workbench-deploy/resource-group-list.png)

6.  在 App Service 的 [基本資訊] 區段中複製 [URL] 值，此值代表您已部署的 Blockchain Workbench 的 Web URL。

    ![應用程式服務基本資訊](media/blockchain-workbench-deploy/app-service.png)

若要將自訂網域名稱與 Blockchain Workbench 產生關聯，請參閱[在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱](../app-service/web-sites-traffic-manager-custom-domain-name.md)。

## <a name="configuring-the-reply-url"></a>設定回覆 URL

在 Azure Blockchain Workbench 部署完成後，下一個步驟是要確定 Azure Active Directory (Azure AD) 用戶端應用程式已註冊至已部署的 Blockchain Workbench Web URL 的正確 [回覆 URL]。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 確認您在用來註冊 Azure AD 用戶端應用程式的租用戶中。
3. 在左側導覽窗格中，選取 [Azure Active Directory] 服務。 選取 [應用程式註冊]。
4. 選取您在 [必要條件] 區段中註冊的 Azure AD 用戶端應用程式。
5. 選取 [設定] > [回覆 URL]。
6. 指定您在**取得 Azure Blockchain Workbench Web URL** 一節中擷取的 Azure Blockchain Workbench 部署的主要 Web URL。 回覆 URL 前面會加上 `https://`。 例如， `https://myblockchain2-7v75.azurewebsites.net`

    ![回覆 URL](media/blockchain-workbench-deploy/configure-reply-url.png)

7. 選取 [儲存] 以更新用戶端註冊。

## <a name="remove-a-deployment"></a>移除部署

當部署不再需要時，您可以藉由刪除 Blockchain Workbench 資源群組來移除部署。

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]，然後選取您想要刪除的資源群組。 
2. 選取 [刪除資源群組]。 輸入資源群組名稱並選取 [刪除]，以確定進行刪除。

    ![刪除資源群組](media/blockchain-workbench-deploy/delete-resource-group.png)

## <a name="next-steps"></a>後續步驟

在這篇操作說明文章中，您已部署了 Azure Blockchain Workbench。 若要了解如何建立區塊鏈應用程式，請繼續閱讀下一篇操作說明文章。

> [!div class="nextstepaction"]
> [在 Azure Blockchain Workbench 中建立區塊鏈應用程式](blockchain-workbench-create-app.md)
