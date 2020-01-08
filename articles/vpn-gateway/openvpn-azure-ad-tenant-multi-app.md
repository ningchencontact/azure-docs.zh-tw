---
title: VPN 閘道：不同使用者群組的 Azure AD 租使用者： Azure AD 驗證
description: 您可以使用 P2S VPN，使用 Azure AD authentication 來連線到您的 VNet
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477170"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>建立 P2S OpenVPN 通訊協定連接的 Azure Active Directory 租使用者

連線到您的 VNet 時，您可以使用以憑證為基礎的驗證或 RADIUS 驗證。 不過，當您使用 Open VPN 通訊協定時，您也可以使用 Azure Active Directory 驗證。 如果您想要讓不同的使用者集合能夠連線到不同的 VPN 閘道，您可以在 AD 中註冊多個應用程式，並將它們連結至不同的 VPN 閘道。 本文可協助您設定 P2S OpenVPN authentication 的 Azure AD 租使用者，並在 Azure AD 中建立並註冊多個應用程式，以允許不同的使用者和群組進行不同的存取。

> [!NOTE]
> Azure AD 驗證僅支援 OpenVPN® 通訊協定連線。
>

## <a name="tenant"></a>1. 建立 Azure AD 租使用者

使用[建立新的租](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)使用者文章中的步驟，建立 Azure AD 租使用者：

* 組織名稱
* 初始網域名稱

範例：

   ![新增 Azure AD 租使用者](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. 建立 Azure AD 租使用者使用者

接下來，建立兩個使用者帳戶。 建立一個全域系統管理員帳戶和一個主要使用者帳戶。 主要使用者帳戶會當做您的主要內嵌帳戶（服務帳戶）使用。 當您建立 Azure AD 租使用者使用者帳戶時，您會針對您想要建立的使用者類型調整目錄角色。

使用[本文](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟，為您的 Azure AD 租使用者建立至少兩個使用者。 請務必變更**目錄角色**以建立帳戶類型：

* 全域管理員
* User

## <a name="enable-authentication"></a>3. 在 Azure AD 租使用者中註冊 Azure VPN 用戶端

1. 找出您要用於驗證之目錄的目錄識別碼。 它會列在 [Active Directory] 頁面的 [屬性] 區段中。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 複製 [目錄識別碼]。

3. 以指派**全域管理員**角色的使用者身分登入 Azure 入口網站。

4. 接下來，請授與系統管理員同意。 在瀏覽器的網址列中，複製並貼上您部署位置的相關 URL：

    公用

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure 政府機構

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 若出現提示，請選取**全域管理員**帳戶。

    ![目錄識別碼](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 出現提示時，選取 [**接受**]。

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在您的 Azure AD 下的 [**企業應用程式**] 中，您會看到列出**Azure VPN** 。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. 為各種使用者/群組註冊額外的應用程式

1. 在您的 Azure Active Directory 底下，依序按一下 [**應用程式註冊**] 和 [ **+ 新增註冊**]。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在 [**註冊應用程式**] 分頁中，輸入**名稱**並選取所需的**支援帳戶類型**，然後按一下 [**註冊**]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 註冊新的應用程式之後，按一下 [應用程式] 分頁底下的 [**公開 API** ]

4. 按一下 [ **+ 新增領域**]
5. 保留預設的 [**應用程式識別碼 URI** ]，然後按一下 [**儲存並繼續**]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. 填寫必要的欄位，並確定**狀態**為 [**已啟用**]。 按一下 [**新增領域**]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. 按一下 [**公開 API** ]，然後按 [**新增用戶端應用程式**]。  針對 [**用戶端識別碼**]，輸入下列值（視雲端而定）：
    -   輸入**41b23e61-6c1e-4545-b367-cd054e0ed4b4** （適用于 Azure**公用**）
    -   針對 Azure**政府**機構輸入**51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    -   針對 Azure**德國**輸入**538ee9e6-310a-468d-afef-ea97365856a9**
    -   輸入適用于 Azure**中國世紀**的**49f817b6-84ae-4cc0-928c-73f27289b3aa**


8. 按一下 [**新增應用程式**]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. 從 [**總覽**] 頁面複製 [**應用程式（用戶端）識別碼**]。 您將需要它來設定您的 VPN 閘道

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重複本節中的步驟（4），建立您的安全性需求所需的多個應用程式。 每個應用程式都會與 VPN 閘道相關聯，而且可以有一組不同的使用者。 只有一個應用程式可以與閘道建立關聯。

## <a name="enable-authentication"></a>5. 將使用者指派給您的應用程式

1. 在 [Azure AD]、[**企業應用程式**] 下，選取新註冊的應用程式，然後按一下 [**屬性**]。 確定 [**需要使用者指派嗎？** ] 設定為 **[是]** 。 按一下 [儲存]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在 [應用程式] 頁面上，依序按一下 [**使用者和群組**] 和 [**新增使用者**]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. 在 [**新增指派**] 底下，按一下 [**使用者和群組**]。 選取您想要能夠存取此 VPN 應用程式的使用者。 按一下 [選取]

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. 在 VPN 閘道上啟用 Azure AD 驗證

1. 藉由執行下列命令，在 VPN 閘道上啟用 Azure AD 驗證，請務必修改命令以反映您自己的環境：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> 在上述命令中，請勿使用 Azure VPN 用戶端的應用程式識別碼，因為它會將 VPN 閘道的存取權授與所有使用者。 使用您所註冊之應用程式的識別碼。

2. 執行下列命令來建立並下載設定檔。 變更-ResourcGroupName 和-Name 值，以符合您自己的值。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. 執行命令之後，您會看到類似下面的結果。 將結果 URL 複製到您的瀏覽器，以下載設定檔 zip 檔案。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. 將下載的 zip 檔案解壓縮。

5. 流覽至解壓縮的 "AzureVPN" 資料夾。

6. 記下 "azurevpnconfig" 檔案的位置。 Azurevpnconfig 包含 VPN 連線的設定，而且可以直接匯入至 Azure VPN 用戶端應用程式。 您也可以將此檔案散發給所有需要透過電子郵件或其他方式連接的使用者。 使用者需要有效的 Azure AD 認證，才能成功連接。

## <a name="next-steps"></a>後續步驟

若要連線到您的虛擬網路，您必須建立並設定 VPN 用戶端設定檔。 請參閱[設定 vpn 用戶端以進行 P2S vpn](openvpn-azure-ad-client.md)連線。
