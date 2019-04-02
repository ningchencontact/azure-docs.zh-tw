---
title: 什麼是 Windows 虛擬桌面預覽版？  - Azure
description: Windows 虛擬桌面預覽版的概觀。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 4443d71d5c0b84ac7dbc18129338229c40323d2c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401044"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>什麼是 Windows 虛擬桌面預覽版？ 

Windows 虛擬桌面預覽版是可在雲端執行的桌面和應用程式虛擬化服務，現在以公開預覽方式提供。

以下是您在 Azure 上執行 Windows 虛擬桌面時可以執行的作業：

* 設定多工作階段的 Windows 10 部署，以提供具有延展性的完整 Windows 10
* 將 Office 365 ProPlus 虛擬化，並使其最佳化以在多使用者的虛擬案例中執行
* 提供具有免費擴充安全性更新的 Windows 7 虛擬桌面
* 將您現有的遠端桌面服務 (RDS) 和 Windows Server 桌面和應用程式帶到任何電腦
* 將桌面和應用程式虛擬化
* 透過統一的管理體驗管理 Windows 10、Windows Server 和 Windows 7 桌面和應用程式

## <a name="key-capabilities"></a>主要功能

透過 Windows 虛擬桌面，您可以設定可調整且具有彈性的環境：

* 在您的 Azure 訂用帳戶中建立完整桌面虛擬化環境，而不必執行任何額外的閘道伺服器。
* 發佈您所需數量的主機集區，以因應您的各種工作負載。
* 自備用於生產工作負載的映像，或從 Azure 資源庫進行測試。
* 利用集區式、多重工作階段的資源降低成本。 使用 Windows 伺服器上 Windows 虛擬桌面和遠端桌面工作階段主機 (RDSH) 角色專用的新 Windows 10 企業版多重工作階段功能，可以大幅降低虛擬機器數目和作業系統 (OS) 額外負荷，同時仍為使用者提供相同的資源。
* 透過個人 (持續性) 桌面提供個別擁有權。

您可以部署及管理虛擬桌面：

* 使用 Windows 虛擬桌面 PowerShell 和 REST 介面，設定主機集區、建立應用程式群組、指派使用者，以及發佈資源。
* 從單一主機集區發佈完整桌面或個別遠端應用程式、為不同的使用者集合建立個別的應用程式群組，或甚至將使用者指派給多個應用程式群組，以減少映像數目。
* 如同您管理您的環境，使用內建委派的存取權來指派角色和收集診斷，以了解各種組態或使用者錯誤。
* 使用新的診斷服務來進行錯誤疑難排解。
* 只要管理映像和虛擬機器，而不管理基礎結構。 您不需要像處理遠端桌面服務一樣，親自管理遠端桌面角色，只要管理您 Azure 訂用帳戶中的虛擬機器。

您也可以指派使用者並將其連線到您的虛擬桌面：

* 指派之後，使用者即可啟動任何 Windows 虛擬桌面用戶端，以將使用者連線到其發佈的 Windows 桌面和應用程式。 從任何裝置透過裝置上的原生應用程式或 Windows 虛擬桌面 HTML5 web 用戶端進行連線。
* 透過服務反向連線安全地建立使用者，因此您永遠不必讓任何輸入連接埠保持開啟。

## <a name="requirements"></a>需求

您需要進行 Windows 虛擬桌面的一些設定，才能讓使用者成功連線到其 Windows 桌面和應用程式。

首先，根據您打算部署的桌面和應用程式，確定您的使用者有適當的授權：

|作業系統|必要授權|
|---|---|
|Windows 10 企業版的多重工作階段或 Windows 10 的單一工作階段|Microsoft E3、E5、A3、A5、Business<br>Windows E3、E5、A3、A5|
|Windows 7|Microsoft E3、E5、A3、A5、Business<br>Windows E3、E5、A3、A5|
|Windows Server 2012 R2、2016、2019|具有軟體保證的 RDS 用戶端存取使用權 (CAL)|

您的基礎結構需要下列項目才能支援 Windows 虛擬桌面：

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* 與 Azure Active Directory 同步的 Windows Server Active Directory。 這可透過以下項目啟用：
  * Azure AD Connect
  * Azure AD 網域服務
* Azure 訂用帳戶，含有包含或已連線到 Windows Server Active Directory 的虛擬網路
  
您為 Windows 虛擬桌面建立的 Azure 虛擬機器必須：

* [已加入標準網域](https://docs.microsoft.com/microsoft-desktop-optimization-pack/appv-v4/domain-joined-and-non-domain-joined-clients)或[已加入混合式 AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)。 虛擬機器無法加入 Azure AD。
* 執行下列其中一個支援的 OS 映像：
  * Windows 10 企業版的多重工作階段
  * Windows Server 2016

>[!NOTE]
>如果需要 Azure 訂用帳戶，您可以[註冊一個月的免費試用](https://azure.microsoft.com/free/)。 如果您使用 Azure 免費試用版，您應該使用 Azure AD Domain Services 讓您的 Windows Server Active Directory 與 Azure Active Directory 保持同步。

Windows 虛擬桌面包含您交付給使用者的 Windows 桌面與應用程式，以及由 Microsoft 在 Azure 上裝載為服務的管理解決方案。 在公開預覽期間，桌面和應用程式都可以部署在任何 Azure 區域的虛擬機器 (VM) 中，而這些 VM 的管理解決方案和資料都會位於美國 (美國東部 2 區域)。 當您在公開預覽期間測試服務時，這可能會導致資料轉送至美國。 我們會開始將管理解決方案和資料當地語系化擴充至所有開始公開推出的 Azure 區域。

為了達到最佳效能，請確定您的網路符合下列需求：

* 從用戶端的網路到主機集區部署所在 Azure 區域的來回行程 (RTT) 延遲應少於 150 毫秒。
* 當裝載桌面和應用程式的 VM 連線至管理服務時，網路流量可能會送到國界以外。
* 若要將網路效能最佳化，我們建議讓工作階段主機的 VM 共置在與管理服務相同的 Azure 區域中。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。 我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。

## <a name="next-steps"></a>後續步驟

若要開始使用，您必須建立租用戶。 若要深入了解如何建立租用戶，請繼續進行租用戶建立教學課程。

> [!div class="nextstepaction"]
> [在 Windows 虛擬桌面預覽版中建立租用戶](tenant-setup-azure-active-directory.md)
