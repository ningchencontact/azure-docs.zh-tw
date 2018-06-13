---
title: 快速入門 - 在 Azure 入口網站中建立 Windows VM | Microsoft Docs
description: 在本快速入門中，您會了解如何使用 Azure 入口網站來建立 Windows 虛擬機器
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c28686c3b6494a0cf8938d39ab9b8338de7aa0c1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012575"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>快速入門：在 Azure 入口網站中建立 Windows 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器 (VM)。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立 VM 及其相關聯的資源。 本快速入門說明如何使用 Azure 入口網站，在 Azure 中部署執行 Windows Server 2016 的虛擬機器 (VM)。 若要查看作用中的 VM，接著要以 RDP 連線至 VM，並安裝 IIS 網頁伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

在 https://portal.azure.com 上登入 Azure 入口網站。

## <a name="create-virtual-machine"></a>建立虛擬機器

1. 選擇 Azure 入口網站左上角的 [建立資源]。

2. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋並選取**Windows Server 2016 Datacenter**，然後選擇 [建立]。

3. 提供 VM 名稱 (例如 *myVM*)，將磁碟類型保留為 *SSD*，然後提供使用者名稱 (例如 *azureuser*)。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)

5. 選擇 [新建] 資源群組，然後提供名稱 (例如 *myResourceGroup*)。 輸入您所需的 [位置]，然後選取 [確定]。

4. 選取 VM 的大小。 您可以依 [計算類型] 或 [磁碟類型] 進行篩選 (舉例而言)。 建議的 VM 大小為 *D2s_v3*。

    ![顯示 VM 大小的螢幕擷取畫面](./media/quick-create-portal/create-windows-vm-portal-sizes.png)

5. 在 [設定] 下保留預設值，然後選取 [確定]。

6. 在 [摘要] 頁面上選取 [建立]，以開始進行 VM 部署。

7. VM 會釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

建立與虛擬機器的遠端桌面連線。 這些指示可為您指出如何從 Windows 電腦連線至您的 VM。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

1. 按一下虛擬機器屬性頁面上的 [連線] 按鈕。 

    ![從入口網站連線至 Azure VM](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png)
    
2. 在 [連線至虛擬機器] 頁面中，保留以 DNS 名稱透過連接埠 3389 進行連線的預設選項，然後按一下 [下載 RDP 檔案]。

2. 開啟下載的 RDP 檔案，然後在出現提示時按一下 [連線]。 

3. 在 [Windows 安全性] 視窗中，選取 [更多選擇]，然後選取 [使用不同的帳戶]。 輸入使用者名稱 *vmname*\*username*，輸入您為虛擬機器建立的密碼，然後按一下 [確定]。

4. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。

## <a name="install-web-server"></a>安裝 Web 伺服器

若要查看作用中的 VM，請安裝 IIS 網頁伺服器。 在 VM 上開啟 PowerShell 提示字元並執行下列命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

完成時，關閉與 VM 的 RDP 連線。

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80

網路安全性群組 (NSG) 可保護輸入和輸出流量。 從 Azure 入口網站建立 VM 時，會在連接埠 3389 上建立 RDP 連線的輸入規則。 由於此 VM 主控 Web 伺服器，因此必須針對連接埠 80 建立 NSG 規則。

1. 在 VM 概觀頁面上，選取 [網路]。
2. 此時會顯示現有輸入和輸出規則的清單。 選擇 [新增輸入連接埠規則]。
3. 選取位於頂端的 [基本] 選項，然後從可用服務清單中選擇 [HTTP]。 系統會為您提供連接埠 80、優先順序和名稱。
4. 若要建立規則，請選取 [新增]。

## <a name="view-the-iis-welcome-page"></a>檢視 IIS 歡迎使用頁面

安裝 IIS 後，現在經由網際網路在您的 VM 上開啟連接埠 80，請使用所選的網頁瀏覽器來檢視預設 IIS 歡迎使用畫面。 使用上一個步驟所取得 VM 的公用 IP 位址。 下列範例示範預設的 IIS 網站：

![IIS 預設網站](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]，然後確認要刪除的資源群組名稱。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、對網路流量開啟網路連接埠，以及安裝基本的網頁伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)
