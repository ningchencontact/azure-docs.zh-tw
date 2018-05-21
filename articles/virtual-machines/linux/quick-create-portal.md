---
title: 快速入門 - 在 Azure 入口網站中建立 Linux VM | Microsoft Docs
description: 在本快速入門中，您將了解如何使用 Azure 入口網站來建立 Linux 虛擬機器
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 18ac0291bff2c0fbfffdd5dfa3097f8a6acb561f
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Linux 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器 (VM)。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立 VM 及其相關聯的資源。 本快速入門說明如何使用 Azure 入口網站，在 Azure 中部署執行 Ubuntu 的 Linux 虛擬機器 (VM)。 若要查看作用中的 VM，接著要以 SSH 連線至 VM，並安裝 NGINX 網頁伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-ssh-key-pair"></a>建立 SSH 金鑰組

您必須要有 SSH 金鑰組，才能完成本快速入門的操作。 如果現有的 SSH 金鑰組，則可略過此步驟。

若要建立 SSH 金鑰組並登入 Linux VM，請從 Bash 殼層執行下列命令，並依照螢幕上的指示操作。 例如，您可以使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 或[適用於 Linux 的 Windows Substem](/windows/wsl/install-win10)。 命令輸出包含公開金鑰檔案的檔案名稱。 將公開金鑰檔案 (`cat ~/.ssh/id_rsa.pub`) 的內容複製到剪貼簿：

```bash
ssh-keygen -t rsa -b 2048
```

如需如何建立 SSH 金鑰的詳細資訊 (包括 PuTTy 的用法)，請參閱[對 Windows 使用 SSH 金鑰](ssh-from-windows.md)。

## <a name="log-in-to-azure"></a>登入 Azure

在 http://portal.azure.com 上登入 Azure 入口網站

## <a name="create-virtual-machine"></a>建立虛擬機器

1. 選擇 Azure 入口網站左上角的 [建立資源]。

2. 在 Azure Marketplace 資源清單上方的搜尋方塊中，依 Canonical 搜尋並選取**Ubuntu Server 16.04 LTS**，然後選擇 [建立]。

3. 提供 VM 名稱 (例如 *myVM*)，將磁碟類型保留為 *SSD*，然後提供使用者名稱 (例如 *azureuser*)。

4. . 針對 [驗證類型] 選取 [SSH 公開金鑰]，然後將您的公開金鑰貼到文字方塊中。 請謹慎地移除公開金鑰中的任何前置或尾端的空白字元。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/quick-create-portal/create-vm-portal-basic-blade.png)

5. 選擇 [新建] 資源群組，然後提供名稱 (例如 *myResourceGroup*)。 輸入您所需的 [位置]，然後選取 [確定]。

4. 選取 VM 的大小。 您可以依 [計算類型] 或 [磁碟類型] 進行篩選 (舉例而言)。 建議的 VM 大小為 *D2s_v3*。

    ![顯示 VM 大小的螢幕擷取畫面](./media/quick-create-portal/create-linux-vm-portal-sizes.png)

5. 在 [設定] 下保留預設值，然後選取 [確定]。

6. 在 [摘要] 頁面上選取 [建立]，以開始進行 VM 部署。

7. VM 會釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

建立與 VM 的 SSH 連線。

1. 在 VM 的 [概觀] 頁面上選取 [連線] 按鈕。 

    ![入口網站 9](./media/quick-create-portal/portal-quick-start-9.png)

2. 在 [連線至虛擬機器] 頁面中，保留以 DNS 名稱透過連接埠 22 進行連線的預設選項。 在**使用 VM 本機帳戶登入**中，會顯示連線命令。 按一下按鈕以複製該命令。 下列範例說明 SSH 連線命令的內容：

    ```bash
    ssh azureuser@myvm-123abc.eastus.cloudapp.azure.com
    ```

3. 將 SSH 連線命令貼到殼層中 (例如 Azure Cloud Shell 或 Windows 上 Ubuntu 的 Bash)，以建立連線。 

## <a name="install-web-server"></a>安裝 Web 伺服器

若要查看作用中的 VM，請安裝 NGINX 網頁伺服器。 若要更新套件來源並安裝最新的 NGINX 套件，請從 SSH 工作階段執行下列命令：

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成時，請 `exit` SSH 工作階段並返回 Azure 入口網站中的 VM 屬性。

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80

網路安全性群組 (NSG) 可保護輸入和輸出流量。 從 Azure 入口網站建立 VM 時，會在連接埠 22 上建立 SSH 連線的輸入規則。 由於此 VM 主控 Web 伺服器，因此必須針對連接埠 80 建立 NSG 規則。

1. 在 VM 概觀頁面上，選取 [網路]。
2. 此時會顯示現有輸入和輸出規則的清單。 選擇 [新增輸入連接埠規則]。
3. 選取位於頂端的 [基本] 選項，然後從可用服務清單中選擇 [HTTP]。 系統會為您提供連接埠 80、優先順序和名稱。
4. 若要建立規則，請選取 [新增]。

## <a name="view-the-web-server-in-action"></a>檢視作用中的網頁伺服器

安裝 NGINX 後，且連接埠 80 對您的 VM 開啟，即可立即從網際網路存取網頁伺服器。 開啟 Web 瀏覽器，並輸入 VM 的公用 IP 位址。 您可以在 VM 的 [概觀] 頁面找到公用 IP 位址，或在您新增輸入連接埠規則的 [網路] 頁面頂端找到。

![預設 NGINX 網站](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]，然後確認要刪除的資源群組名稱。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、建立網路安全性群組和規則，並已安裝基本的 Web 伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Linux VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Linux 虛擬機器教學課程](./tutorial-manage-vm.md)
