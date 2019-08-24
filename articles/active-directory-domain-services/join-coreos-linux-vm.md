---
title: Azure Active Directory Domain Services：加入 CoreOS Linux VM |Microsoft Docs
description: 將 CoreOS Linux 虛擬機器加入 Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: dc76d9a0d492d8ef0e37c0c34173216ff4c75164
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990575"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>將 CoreOS Linux 虛擬機器加入受控網域
本文說明如何將 Azure 中的 CoreOS Linux 虛擬機器加入 Azure AD Domain Services 受控網域。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始之前
若要執行本文中所列的工作，您需要︰
1. 有效的 **Azure 訂用帳戶**。
2. **Azure AD 目錄** - 與內部部署目錄或僅限雲端的目錄同步處理。
3. **Azure AD 網域服務** 必須已針對 Azure AD 目錄啟用。 如果還沒有啟用，請按照 [入門指南](tutorial-create-instance.md)所述的所有工作來進行。
4. 確保您尚未將受控網域的 IP 位址設定為虛擬網路的 DNS 伺服器。 如需詳細資訊，請參閱[如何更新 Azure 虛擬網路的 DNS 設定](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. 完成[將密碼同步處理至您的 Azure AD Domain Services 受控網域](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)所需的步驟。


## <a name="provision-a-coreos-linux-virtual-machine"></a>佈建 CoreOS Linux 虛擬機器
使用下列任一個方法，在 Azure 中佈建 CoreOS 虛擬機器：
* [Azure 入口網站](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

本文會在 Azure 中使用 **CoreOS Linux (穩定)** 虛擬機器映像。

> [!IMPORTANT]
> * 將虛擬機器部署至**已在啟用 Azure AD Domain Services 的相同虛擬網路**。
> * 挑選與您已啟用 Azure AD Domain Services 的子網路**不同的子網路**。
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>遠端連線到新佈建的 Linux 虛擬機器
CoreOS 虛擬機器已佈建在 Azure 中。 下一個工作是使用佈建 VM 時所建立的本機系統管理員帳戶，從遠端連線至虛擬機器。

依照[如何登入執行 Linux 的虛擬機器一](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)文中的指示進行。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>在 Linux 虛擬機器上設定主機檔案
在您的 SSH 終端機中，編輯 /etc/hosts 檔案並更新您的電腦 IP 位址和主機名稱。

```console
sudo vi /etc/hosts
```

在主機檔案中，輸入下列值：

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

在這裡, ' contoso.com ' 是受控網域的 DNS 功能變數名稱。 'contoso-coreos' 是您要加入至受控網域之 CoreOS 虛擬機器的主機名稱。


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>在 Linux 虛擬機器上設定 SSSD 服務
接著，更新 ('/ etc/sssd/sssd.conf') 中的 SSSD 設定檔以符合下列範例：

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

取代 ' CONTOSO。COM ' 與受控網域的 DNS 功能變數名稱。 確定您在 conf 檔案中指定大寫的網域名稱。


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>將 Linux 虛擬機器加入受控網域
既然 Linux 虛擬機器上已安裝必要的封裝，下一個工作是將虛擬機器加入受控網域。

```console
sudo adcli join -D CONTOSO.COM -U bob@CONTOSO.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **疑難排解：** 如果 *adcli* 找不到您的受控網域：
>   * 確定可從虛擬機器觸達網域 (請嘗試 ping)。
>   * 檢查虛擬機器已確實部署到有提供受控網域的相同虛擬網路上。
>   * 查看您是否已更新虛擬網路的 DNS 伺服器設定，以指向受控網域的網域控制站。

啟動 SSSD 服務。 在 SSH 終端機中輸入下列命令：
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>確認加入網域
確認電腦是否已成功加入受控網域。 使用不同的 SSH 連線來連線到加入網域的 CoreOS VM。 使用網域使用者帳戶，然後查看使用者帳戶是否解析正確。

1. 在 SSH 終端機中輸入下列命令，以使用 SSH 連線到加入網域的 CoreOS 虛擬機器。 使用屬於受控網域的網域帳戶 (例如，在此例中為 'bob@CONTOSO.COM')。
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-coreos.contoso.com
    ```

2. 在 SSH 終端機中輸入下列命令，以查看是否已正確初始化主目錄。
    
    ```console
    pwd
    ```

3. 在 SSH 終端機中輸入下列命令，以查看是否會正確解析群組成員資格。
   
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>針對加入網域進行疑難排解
請參閱 [針對加入網域進行疑難排解](join-windows-vm.md#troubleshoot-domain-join-issues) 一文。

## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](tutorial-create-instance.md)
* [將 Windows Server 虛擬機器加入 Azure Active Directory Domain Services 受控網域](active-directory-ds-admin-guide-join-windows-vm.md)
* [如何登入執行 Linux 的虛擬機器](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
