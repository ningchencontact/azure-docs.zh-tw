---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854559"
---
[Azure 檔案儲存體](../articles/storage/files/storage-files-introduction.md)透過[AZURE ACTIVE DIRECTORY DOMAIN SERVICES (Azure AD DS)](../articles/active-directory-domain-services/overview.md), 在伺服器訊息區 (SMB) 上支援以身分識別為基礎的驗證。 已加入網域的 Windows 虛擬機器 (Vm) 可以使用[Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md)認證來存取 Azure 檔案共用。

您可以使用[角色型存取控制 (RBAC)](../articles/role-based-access-control/overview.md)來管理身分識別的 Azure 檔案儲存體共用層級存取, 例如 Azure AD 中的使用者或群組。 您可以定義自訂的 RBAC 角色, 其中包含用來存取 Azure 檔案儲存體的通用許可權集合。 在指派自訂 RBAC 角色給 Azure AD 身分識別時，會根據這些權限將 Azure 檔案共用的存取權授予身分識別。

Azure 檔案儲存體也支援在檔案共用中的所有檔案和目錄上保留、繼承和強制執行[NTFS dacl](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) 。 如果您是從檔案共用將資料複製到 Azure 檔案服務，或從 Azure 檔案服務將資料複製到檔案共用，都可以指定維持 NTFS DACL。 如此一來, 您就可以使用 Azure 檔案儲存體來執行備份案例, 並在您的內部部署檔案共用與雲端檔案共用之間保留您的 NTFS DACL。 

> [!NOTE]
> - Linux Vm 不支援伺服器訊息區 (SMB) 存取的 Azure AD DS 驗證。 只支援 Windows VM。
> - Active Directory 已加入網域的電腦不支援 SMB 存取的 Azure AD DS 驗證。 在過渡期間, 請考慮使用[Azure 檔案同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)來開始將您的資料移轉至 Azure 檔案儲存體, 並使用來自內部部署 Active Directory 已加入網域的電腦 Active Directory 認證, 繼續強制執行存取控制。 
> - 只有在2018年9月24日之後建立的儲存體帳戶, 才可以使用 SMB 存取的 Azure AD DS 驗證。
> - Azure 檔案同步所管理的 Azure 檔案共用不支援 SMB 存取和 NTFS DACL 持續性 Azure AD DS 驗證。
> - Azure AD DS 驗證不支援對 Azure AD DS 中建立的電腦帳戶進行驗證。
