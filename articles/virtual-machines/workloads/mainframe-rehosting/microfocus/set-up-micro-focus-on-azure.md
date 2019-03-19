---
title: 在 Azure 上安裝 Micro Focus Enterprise Server 4.0 和企業開發人員 4.0 |Microsoft Docs
description: 重新裝載您 IBM z/OS 主機的工作負載使用 Micro Focus 開發及測試 Azure 虛擬機器 (Vm) 上的環境。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 46b135aabaefb3a94e4470927297fb696f216b7a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192525"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>在 Azure 上安裝 Micro Focus Enterprise Server 4.0 和企業開發人員 4.0

本文說明如何設定[Micro 焦點 Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/)並[Micro 焦點企業開發人員 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/)在 Azure 上。

常見的工作負載，在 Azure 上是開發和測試環境，因為它是因此符合成本效益且易於部署與卸除。 與 Enterprise Server Micro Focus 已建立的最大大型主機重新裝載平台提供的其中一個。 您可以在成本較低的 x86 上執行 z/OS 的工作負載使用 Windows 或 Linux 虛擬機器 (Vm) 在 Azure 上的平台。

此安裝程式會使用執行 Windows Server 2016 映像從 Azure Marketplace 使用 Microsoft SQL Server 2017 已安裝的 Azure Vm。 這項設定也適用於 Azure Stack。

對應的開發環境，適用於 Enterprise Server 是企業開發人員，在其中一個 Microsoft Visual Studio 2017，Visual Studio Community （免費下載） 執行，或 Eclipse。 這篇文章會示範如何使用隨附於安裝 Visual Studio 2017 的 Windows Server 2016 虛擬機器進行部署。

## <a name="prerequisites"></a>必要條件

開始之前，請參閱這些必要條件：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Micro Focus 軟體並有效的授權 （或試用版授權）。 如果您是現有的 Micro Focus 客戶，請連絡您的 Micro Focus 代表。 否則，請[要求試用版](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)。

- 取得的文件[企業伺服器與企業開發人員](https://www.microfocus.com/documentation/enterprise-developer/#")。

> [!NOTE]
> 最佳做法是讓您能夠控制其存取 Azure Vm，設定站對站虛擬私人網路 (VPN) 通道或 jumpbox。

## <a name="install-enterprise-server"></a>安裝企業伺服器

1. 取得較佳的安全性和管理能力，請考慮建立新的資源群組，只針對此專案，例如**RGMicroFocusEntServer**。 在 Azure 中使用名稱的第一個部分，以指定要讓它更容易找出清單中的資源類型。

2. 建立虛擬機器。 從 Azure Marketplace 中，選取虛擬機器和您想要的作業系統。 以下是建議的安裝程式：

    - **企業伺服器**:選取 ES2 v3 VM （含 2 個 Vcpu 和 16 GB 記憶體） 與 Windows Server 2016 和 SQL Server 2017 安裝。 您也可以從 Azure Marketplace 內使用此映像。 企業伺服器也可以使用 Azure SQL Database。

    - **企業開發人員**:選取 B2ms VM （2 個 Vcpu 和 8 GB 記憶體） 與 Windows 10 和安裝 Visual Studio。 您也可以從 Azure Marketplace 內使用此映像。

3. 在 **基本概念**刀鋒視窗中，輸入您的使用者名稱和密碼。 選取 **訂用帳戶**並**位置 （地區）** 您想要使用的 vm。 選取  **RGMicroFocusEntServer**的資源群組。

4. 因此它們可以彼此通訊，請將這兩個 Vm 放入相同的虛擬網路中。

5. 接受其餘設定的預設值。 請記得使用者名稱和您建立這些 Vm 的系統管理員的密碼。

6. 虛擬機器建立後，開啟輸入連接埠 9003，arm、x86 和 http 的 80 和 3389 Enterprise Server 電腦上的 rdp 和開發人員電腦上的 3389。

7. 若要登入 Azure 入口網站中的企業伺服器虛擬機器選取 ES2 v3 VM。 移至**概觀**刀鋒視窗，然後選取**Connect**啟動的 RDP 工作階段。 使用您建立 vm 的認證登入。

8. 從 RDP 工作階段中，載入下列兩個檔案。 由於這是 Windows，因此您可以拖放到 RDP 工作階段檔案：

    - **es\_40.exe**，企業伺服器安裝檔案。

    - **mflic**，對應的授權檔案 — 企業伺服器將不會載入而不需要它。

9. 按兩下檔案以開始安裝。 在第一個視窗中，選取安裝位置，並接受使用者授權合約。

     ![Micro 焦點 Enterprise Server 安裝程式畫面](media/01-enterprise-server.png)

     安裝完成時，便會出現下列訊息：

     ![Micro 焦點 Enterprise Server 安裝程式畫面](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>檢查更新

安裝之後，請務必檢查有任何其他的更新，因為有一些必要條件，例如與企業伺服器一起安裝的 Microsoft c + + 可轉散發套件和.NET Framework。

### <a name="upload-the-license"></a>上傳的授權

1. 啟動 Micro Focus 授權管理。

2. 按一下 **開始** \> **Micro 焦點 License Manager** \> **授權管理**，然後按一下 **安裝**  索引標籤。選擇要上傳的授權格式類型： 授權檔案或 16 個字元的授權碼。 例如，對於檔案，在**授權檔案**，瀏覽至**mflic**檔案先前上傳至該 VM 並選取**安裝授權**。

     ![Micro 焦點授權管理對話方塊](media/03-enterprise-server.png)

3. 請確認企業伺服器載入。 嘗試啟動企業伺服器的系統管理站台，從使用此 URL 的瀏覽器 <http://localhost:86/> 。 Enterprise Server 管理 頁面會顯示所示。

     ![Enterprise Server 管理 頁面](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>開發人員機器上安裝企業開發人員

1. 選取稍早建立的資源群組 (例如**RGMicroFocusEntServer**)，然後選取 開發人員映像。

2. 若要登入虛擬機器，請前往**概觀**刀鋒視窗，然後選取**Connect**。 這會啟動的 RDP 工作階段。 使用您建立 vm 的認證登入。

3. 從 RDP 工作階段中，載入下列兩個檔案 （拖曳和置放，如果您喜歡）：

    - **edvs2017.exe**，企業伺服器安裝檔案。

    - **mflic**，對應的授權檔案 （企業開發人員將不會載入沒有它）。

4. 按兩下**edvs2017.exe**檔案以啟動安裝。 在第一個視窗中，選取安裝位置，並接受使用者授權合約。 如果您想，選擇**安裝 Rumba 9.5**安裝此終端機模擬器，您可能需要。

     ![Micro 焦點企業開發人員，Visual Studio 2017 安裝程式對話方塊](media/04-enterprise-server.png)

5. 安裝程式完成之後，便會出現下列訊息：

     ![安裝成功訊息](media/05-enterprise-server.png)

6. 就像您對企業伺服器，請啟動 [Micro 焦點授權管理員]。 選擇**開始** \> **Micro 焦點 License Manager** \> **授權管理**，然後按一下**安裝** 索引標籤。

7. 選擇要上傳的授權格式類型： 授權檔案或 16 個字元的授權碼。 例如，對於檔案，在**授權檔案**，瀏覽至**mflic**檔案先前上傳至該 VM 並選取**安裝授權**。

     ![Micro 焦點授權管理對話方塊](/edia/07-enterprise-server.png)

企業開發人員載入時，Micro Focus 開發和測試環境，在 Azure 上部署已完成 ！

## <a name="next-steps"></a>後續步驟

- [設定 銀行示範應用程式](./demo.md)
