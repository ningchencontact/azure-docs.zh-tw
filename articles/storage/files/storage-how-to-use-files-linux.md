---
title: 搭配 Linux 使用 Azure 檔案 | Microsoft Docs
description: 了解如何透過 Linux 上的 SMB 掛接 Azure 檔案共用。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 375d0de60b916becc8e86a1e33cf4ed46f12c077
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754828"
---
# <a name="use-azure-files-with-linux"></a>搭配 Linux 使用 Azure 檔案

[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 可以使用 [SMB 核心用戶端](https://wiki.samba.org/index.php/LinuxCIFS)將 Azure 檔案共用裝載在 Linux 發行版本中。 本文將說明掛接 Azure 檔案共用的兩種方式：使用 `mount` 命令的隨選掛接，以及建立項目 `/etc/fstab` 的開機掛接。

> [!NOTE]  
> 若要在 Azure 區域之外掛接 Azure 檔案共用，例如內部部署或是在不同的 Azure 區域，作業系統必須支援 SMB 3.0 的加密功能。

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>以 Linux 掛接 Azure 檔案共用和 cifs-utils 套件的必要條件
<a id="smb-client-reqs"></a>

* **現有的 Azure 儲存體帳戶和檔案共用**:若要完成這篇文章，您需要有儲存體帳戶和檔案共用。 如果您尚未建立一個，請參閱其中一個快速入門：[建立檔案共用-CLI](storage-how-to-use-files-cli.md)。

* **您的儲存體帳戶名稱和金鑰**您需要的儲存體帳戶名稱和金鑰才能完成這篇文章。 如果您建立另一種使用 CLI 快速入門，您應該已經有，否則請參閱 CLI 快速入門中所連結更早版本，以了解如何擷取儲存體帳戶金鑰。

* **選擇 Linux 發行版本以符合掛接需求。**  
      Azure 檔案服務可以透過 SMB 2.1 和 SMB 3.0 掛接。 對於來自用戶端內部部署或其他 Azure 區域的連線，您必須使用 SMB 3.0；Azure 檔案服務會拒絕 SMB 2.1 (或是沒有加密的 SMB 3.0)。 如果您從相同的 Azure 區域內的 VM 存取 Azure 檔案共用，則可以使用 SMB 2.1 存取您的檔案共用，若且唯若，對於裝載 Azure 檔案共用的儲存體帳戶已停用 [需要安全傳輸]  。 我們一律建議需要安全傳輸，並僅使用包含加密的 SMB 3.0。

    SMB 3.0 加密支援是在 Linux 核心版本 4.11 導入，並且針對熱門的 Linux 發行版本反向導入到舊版核心版本。 在本文件發行時，Azure 資源庫的下列發行版本支援掛接此資料表標頭中指定的選項。 

* **具有對應掛接功能的最低建議版本 (SMB 2.1 版與 SMB 3.0 版)。**    

    |   | SMB 2.1 <br>(掛接在相同 Azure 區域內的 VM 上) | SMB 3.0 <br>(從內部部署環境和跨區域掛接) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

    如果此處未列出您的 Linux 發行版本，您可以使用下列命令查看 Linux 核心版本：

   ```bash
   uname -r
   ```

* <a id="install-cifs-utils"></a>**已安裝 cifs-utils 套件。**  
    可使用套件管理員將 cifs-utils 套件安裝在所選擇的 Linux 發行版本上。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt-get` 封裝管理員：

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    在 **RHEL** 和 **CentOS** 上，請使用 `yum` 封裝管理員：

    ```bash
    sudo yum install cifs-utils
    ```

    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他發行版本上，請使用適當的封裝管理員或[從來源編譯](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **決定裝載共用的目錄/檔案使用權限**：下列範例使用權限 `0777` 提供所有使用者的讀取、寫入和執行權限。 您可以將該值與其他[chmod 權限](https://en.wikipedia.org/wiki/Chmod)所需，但這表示可能會限制存取。 如果您使用其他權限，您應該考慮也使用 uid 與 gid，以保留您所選擇的本機群組的存取權。

> [!NOTE]
> 如果未明確指派 dir_mode=0777,file_mode=0777,sec=ntlmssp 和 file_mode 目錄和檔案權限，它們會預設為 0755年。

* **請確定已開啟連接埠 445**：SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 隨需掛接 Azure 檔案共用

1. **[在您的 Linux 發行版本上安裝 cifs-utils 封裝](#install-cifs-utils)** 。

1. **建立裝載點的資料夾**：掛接點的資料夾可以任何位置上建立檔案系統中，但若要建立這個新的資料夾下的常見慣例是。 比方說，下列命令會建立新的目錄，取代 **< storage_account_name >** 並 **< file_share_name >** 為您的環境的適當資訊：

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **使用掛接命令裝載 Azure 檔案共用**：請記得取代 **< storage_account_name >** ， **< share_name >** ， **< smb_version >** ， **< storage_account_key >** ，並 **< mount_point >** 為您的環境的適當資訊。 如果您的 Linux 散發套件支援使用加密的 SMB 3.0 (請參閱[了解 SMB 用戶端需求](#smb-client-reqs)如需詳細資訊)，使用**3.0** for **< smb_version >** 。 不支援 SMB 3.0 加密的 Linux 發行版本，使用**2.1** for **< smb_version >** 。 只可以 Azure 區域之外掛接 Azure 檔案共用 （包括內部部署或在不同的 Azure 區域中） 使用 SMB 3.0。 如有需要，您可以變更掛接的共用的目錄和檔案權限但這表示限制的存取。

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> 使用 Azure 檔案共用後，即可使用 `sudo umount <mount_point>` 取消掛接共用。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 建立 Azure 檔案共用的持續掛接點

1. **[在您的 Linux 發行版本上安裝 cifs-utils 封裝](#install-cifs-utils)** 。

1. **建立裝載點的資料夾**：掛接點的資料夾可以任何位置上建立檔案系統中，但若要建立這個新的資料夾下的常見慣例是。 每當您建立這個資料夾，請記下資料夾的完整路徑。 比方說，下列命令會建立新的目錄，取代 **< storage_account_name >** 並 **< file_share_name >** 為您的環境的適當資訊。

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **建立認證檔來儲存檔案共用的使用者名稱 (儲存體帳戶名稱) 和密碼 (儲存體帳戶金鑰)。** 取代 **< storage_account_name >** 並 **< storage_account_key >** 為您的環境的適當資訊。

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **變更認證檔的權限，因此只有 root 才能讀取或修改密碼檔。** 由於儲存體帳戶金鑰基本上是儲存體帳戶的超級管理員密碼，因此請務必設定只有 root 才能存取的檔案權限，讓更低權限的使用者無法擷取儲存體帳戶金鑰。   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **使用下列命令將下列一行附加至 `/etc/fstab`** ：請記得取代 **< storage_account_name >** ， **< share_name >** ， **< smb_version >** ，和 **< mount_point >** 為您的環境的適當資訊。 如果您的 Linux 散發套件支援使用加密的 SMB 3.0 (請參閱[了解 SMB 用戶端需求](#smb-client-reqs)如需詳細資訊)，使用**3.0** for **< smb_version >** 。 不支援 SMB 3.0 加密的 Linux 發行版本，使用**2.1** for **< smb_version >** 。 只可以 Azure 區域之外掛接 Azure 檔案共用 （包括內部部署或在不同的 Azure 區域中） 使用 SMB 3.0。

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> 您可以使用 `sudo mount -a` 在編輯 `/etc/fstab` 之後掛接 Azure 檔案共用而不需要重新開機。

## <a name="feedback"></a>意見反應

Linux 使用者，歡迎您提供相關資訊！

適用於 Linux 的 Azure 檔案使用者群組提供了一個論壇，讓您在 Linux 上評估並採用檔案儲存體之際，得以分享意見反應。 請寄電子郵件至 [Azure 檔案 Linux 使用者](mailto:azurefileslinuxusers@microsoft.com)，以加入使用者的群組。

## <a name="next-steps"></a>後續步驟

請參閱這些連結，以取得 Azure 檔案服務的詳細資訊：

* [規劃 Azure 檔案部署](storage-files-planning.md)
* [常見問題集](../storage-files-faq.md)
* [疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
