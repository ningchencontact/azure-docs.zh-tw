---
title: Windows 虛擬桌面預覽環境-Azure
description: Windows 虛擬桌面預覽環境的基本項目。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: ceed6a8bb74206b7c6689ce542482148800e4ba9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403505"
---
# <a name="windows-virtual-desktop-preview-environment"></a>Windows 虛擬桌面預覽環境

Windows 虛擬桌面 Preview 是一項服務，可讓使用者輕鬆又安全地存取其虛擬的桌面及 Remoteapp。 本主題會告訴您更多有關於 Windows 虛擬桌面環境的一般結構。

## <a name="tenants"></a>租用戶

Windows 虛擬桌面的租用戶是用於管理 Windows 虛擬桌面環境的主要介面。 每個 Windows 虛擬桌面租用戶必須與包含使用者要登入環境的 Azure Active Directory 產生關聯。 從 Windows 的虛擬桌面租用戶中，您可以開始建立主應用程式集區，以執行您的使用者工作負載。

## <a name="host-pools"></a>主應用程式集區

主應用程式集區是 Azure 虛擬機器，當您執行 Windows 的虛擬桌面代理程式做為工作階段主機註冊到 Windows 虛擬桌面集合。 主應用程式集區中所有工作階段主機虛擬機器應該來自相同的映像，以一致的使用者體驗。

主應用程式集區可以是下列其中一種：

- 個人、 每個工作階段主機指派給個別使用者的位置。
- 共用工作階段主機可以接受來自任何主應用程式集區中的應用程式群組獲授權的使用者連線的位置。

您可以在主應用程式集區，以變更其負載平衡行為，可以採取的每個工作階段主機的工作階段數目和使用者可以執行登入他們的 Windows 虛擬桌面工作階段時，主應用程式集區中的工作階段主機上設定其他屬性。 您控制使用者可透過應用程式群組所發佈的資源。

## <a name="app-groups"></a>應用程式群組

「 應用程式群組是應用程式安裝在主應用程式集區中的工作階段主機的邏輯群組。 「 應用程式群組可以是下列其中一種：

- RemoteApp，讓使用者存取的 Remoteapp 個別選取並發行至應用程式群組
- Desktop 中，供使用者存取完整的桌面

根據預設，傳統型應用程式群組 （名為 「 桌面應用程式群組 」） 會自動建立每當您建立主應用程式集區。 您可以隨時移除此應用程式群組。 不過，您無法建立傳統型應用程式的另一個群組主應用程式集區中的傳統型應用程式群組存在時。 若要發佈的 Remoteapp，您必須建立 RemoteApp 的應用程式群組。 您可以建立多個不同的背景工作的案例的 RemoteApp 應用程式群組。 不同的 RemoteApp 應用程式群組也可以包含重疊的 Remoteapp。

若要發行給使用者的資源，您必須將它們指派給應用程式群組。 當將使用者指派給應用程式群組中，請考慮下列事項：

- 使用者無法指派給桌面應用程式群組和相同的主應用程式集區中的 RemoteApp 應用程式群組中。
- 使用者可以指派給相同的主應用程式集區中的多個應用程式群組，其摘要將會累積的這兩個應用程式群組。

## <a name="tenant-groups"></a>租用戶群組

Windows 虛擬桌面，在 Windows 虛擬桌面的租用戶會是大部分的安裝和設定的地方。 Windows 虛擬桌面的租用戶所包含的主應用程式集區、 應用程式群組和應用程式群組的使用者指派。 不過，可能有某些情況下，您需要管理多個 Windows 虛擬桌面租用戶，特別是如果您是雲端服務提供者 (CSP) 或主控夥伴。 在這些情況下，您可以使用自訂的 Windows 虛擬桌面的租用戶群組，將每個客戶的 Windows 虛擬桌面的租用戶，並集中管理存取權。 不過，如果您只管理單一的 Windows 虛擬桌面租用戶，租用戶群組的概念並不適用，而且您可以繼續操作及管理您的租用戶中的預設租用戶群組存在。

## <a name="end-users"></a>終端使用者

您已指派使用者給其應用程式群組之後，他們可以連線到任何 Windows 虛擬桌面用戶端的 Windows 虛擬桌面部署。

## <a name="next-steps"></a>後續步驟

深入了解委派的存取權，以及如何將角色指派給使用者[委派的存取，在 Windows 虛擬桌面預覽](delegated-access-virtual-desktop.md)。

若要了解如何設定 Windows 虛擬桌面租用戶，請參閱[建立租用戶中 Windows 虛擬桌面預覽](tenant-setup-azure-active-directory.md)。

若要了解如何連線到 Windows 虛擬桌面，請參閱下列文章：

- [連接到 Windows 7 和 Windows 10 的遠端桌面用戶端](connect-windows-7-and-10.md)
- [連線至 Windows 虛擬桌面預覽 web 用戶端](connect-web.md)
