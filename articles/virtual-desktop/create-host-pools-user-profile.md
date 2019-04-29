---
title: 設定使用者設定檔共用 Windows 虛擬桌面預覽主應用程式集區-Azure
description: 如何設定 Windows 虛擬桌面預覽主應用程式集區 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: 0cb4df099faad8ca482fd15cf0bb50504c1528ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883865"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>設定主機集區的使用者設定檔共用

Windows 虛擬桌面預覽服務提供建議的使用者設定檔方案 FSLogix 設定檔的容器。 我們不建議使用使用者設定檔磁碟 (UPD) 解決方案，將會取代在未來版本的 Windows 虛擬桌面。

本節會告訴您如何設定主應用程式集區的 FSLogix 設定檔容器共用。 如 FSLogix 有關的一般文件，請參閱 < [FSLogix 網站](https://docs.fslogix.com/)。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>建立新的虛擬機器會做為檔案共用

建立虛擬機器時，請務必將它放上是相同的虛擬網路的主應用程式集區虛擬機器或虛擬網路可連線至主應用程式集區的虛擬機器上。 您可以透過多種方式來建立虛擬機器：

- [從 Azure 映像庫映像建立虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [從受控映像建立虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [從非受控映像建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

建立虛擬機器之後, 將它加入網域可以執行下列動作：

1. [連接到虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)時建立虛擬機器所提供的認證。
2. 在虛擬機器上啟動**控制台中**，然後選取**系統**。
3. 選取 **電腦名稱**，選取**變更設定**，然後選取 **變更...**
4. 選取 **網域**，然後輸入虛擬網路上的 Active Directory 網域。
5. 驗證程式的電腦加入網域的權限的網域帳戶。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>準備虛擬機器以做為使用者設定檔的檔案共用

以下是有關如何準備虛擬機器的一般指示，以做為使用者設定檔的檔案共用：

1. Windows 虛擬桌面 Active Directory 將使用者加入至[Active Directory 安全性群組](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)。 此安全性群組，將用於驗證的 Windows 虛擬桌面使用者，以您剛建立檔案共用的虛擬機器。
2. [連接到檔案共用的虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)。
3. 在檔案共用的虛擬機器，請上建立資料夾**C 磁碟機**，用以做為設定檔共用。
4. 以滑鼠右鍵按一下新的資料夾中，選取**屬性**，選取**共用**，然後選取**進階共用...**.
5. 選取 **共用此資料夾**，選取**權限...**，然後選取**加入...**.
6. 搜尋您要加入 Windows 虛擬桌面使用者的安全性群組，然後確認該群組具有**完全控制**。
7. 之後新增的安全性群組，以滑鼠右鍵按一下該資料夾中，選取**屬性**，選取**共用**，然後向下複製**網路路徑**供稍後使用。

如需有關權限的詳細資訊，請參閱[FSLogix 文件](https://docs.fslogix.com/display/20170529/Requirements%2B-%2BProfile%2BContainers)。

## <a name="configure-the-fslogix-profile-container"></a>設定 FSLogix 設定檔的容器

若要使用 FSLogix 軟體中設定虛擬機器，請依下列方式向主應用程式集區的每部機器上：

1. [連接到虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)時建立虛擬機器所提供的認證。
2. 啟動網際網路瀏覽器並瀏覽至[此連結](https://go.microsoft.com/fwlink/?linkid=2084562)下載 FSLogix 代理程式。 Windows 虛擬桌面的公開預覽的一部分，您會收到啟用 FSLogix 軟體的授權金鑰。 關鍵在於 FSLogix 代理程式.zip 檔案中所包含的 LicenseKey.txt 檔。
3. 瀏覽至其中一個\\ \\Win32\\版本或\\ \\X64\\.zip 檔案，並在執行中的發行**FSLogixAppsSetup**安裝 FSLogix 代理程式。
4. 瀏覽至**Program Files** > **FSLogix** > **應用程式**以確認安裝的代理程式。
5. 從 [開始] 功能表中，執行**RegEdit**身為系統管理員。 瀏覽至**電腦\\HKEY_LOCAL_MACHINE\\軟體\\FSLogix**。
6. 建立名為索引鍵**設定檔**。
7. 建立設定檔索引鍵的下列值：

| 名稱                | 類型               | 資料/值                        |
|---------------------|--------------------|-----------------------------------|
| Enabled             | DWORD              | 1                                 |
| VHDLocations        | 多字串值 | 「 檔案共用的網路路徑 」     |

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)。