---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269342"
---
[Azure 檔案](../articles/storage/files/storage-files-introduction.md)透過 [Azure Active Directory (Azure AD) Domain Services](../articles/active-directory-domain-services/overview.md)，支援「透過 SMB (伺服器訊息區) (預覽) 進行以身分識別為基礎的驗證」。 您加入網域的 Windows 虛擬機器 (VM) 可以使用 [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) 認證存取 Azure 檔案共用。 

您可以管理 Azure 檔案共用層級存取權的身分識別，例如使用者、 群組與 Azure ad[角色型存取控制 (RBAC)](../articles/role-based-access-control/overview.md)。 您可以定義自訂 RBAC 角色，內有幾組用來存取 Azure 檔案的常用權限集。 在指派自訂 RBAC 角色給 Azure AD 身分識別時，會根據這些權限將 Azure 檔案共用的存取權授予身分識別。

在預覽中，Azure 檔案對所有檔案和檔案共用中的目錄，均支援 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) 的保留、繼承和強制執行。 如果您是從檔案共用將資料複製到 Azure 檔案服務，或從 Azure 檔案服務將資料複製到檔案共用，都可以指定維持 NTFS DACL。 如此一來，您可以使用 Azure 檔案實作備份作業，同時在內部部署檔案共用與雲端檔案共用之間保留您的 NTFS DACL。 

> [!NOTE]
> - 適用於 Linux Vm 不支援的 SMB 存取權的 azure AD 網域服務驗證。 只支援 Windows VM。
> - Active Directory 加入網域的電腦不支援的 SMB 存取權的 azure AD 網域服務驗證。 在此過渡期間，您可以考慮利用[Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)開始將您的資料移轉至 Azure 檔案服務，並繼續強制執行存取控制使用 AD 認證，從您在內部部署 AD 已加入網域的機器。 
> - SMB 存取 azure AD 網域服務驗證是僅適用於在 2018 年 9 月 24 日之後建立的儲存體帳戶。
> - 在受 Azure 檔案同步服務的 Azure 檔案共用上不支援的 SMB 存取與 NTFS DACL 永續性的 azure AD 網域服務驗證。 
