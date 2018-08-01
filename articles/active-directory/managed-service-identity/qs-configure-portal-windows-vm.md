---
title: 如何使用 Azure 入口網站在 Azure VM 上設定受控服務識別
description: 使用 Azure 入口網站在 Azure VM 上設定受控服務識別的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 6ba090065b18a44cc1f01a62eefb5dcf52bcf356
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213260"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>使用 Azure 入口網站設定 VM 受控服務識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure 入口網站啟用和停用 Azure VM 之系統指派的身分識別。 目前不支援透過 Azure 入口網站指派使用者指派身分識別，以及將其從 Azure VM 中移除。

> [!NOTE]
> 目前，不支援透過 Azure 入口網站的使用者指派身分識別作業。 請隨時回來查看是否有更新內容。 

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行本文中的管理作業，您的帳戶需要下列角色指派：
    - [虛擬機器參與者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可從 Azure VM 啟用和移除系統指派的識別。

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>在 Azure vm 建立期間的受控服務識別

目前，透過 Azure 入口網站建立虛擬機器不支援受控服務識別作業。 因此，請參閱下列 VM 建立快速入門文章之一，先建立 VM：

- [使用 Azure 入口網站建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 入口網站建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

然後進入下一節，取得在虛擬機器上啟用受控服務識別的詳細資料。

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用受控服務識別

若要在原本佈建時沒有使用系統指派身分識別的 VM 上啟用此身分識別：

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至所需的虛擬機器，然後選取 [組態] 頁面。

3. 藉由在 [受控服務識別] 下選取 [是]，以在虛擬機器上啟用系統指派的身分識別，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

   > [!NOTE]
   > 不支援透過 Azure 入口網站將使用者指派的身分識別新增至虛擬機器。

   ![組態頁面螢幕擷取畫面](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>將受控服務識別從 Azure VM 中移除

如果您的虛擬機器不再需要系統指派的身分識別：

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 

2. 瀏覽至所需的虛擬機器，然後選取 [組態] 頁面。

3. 藉由在 [受控服務識別] 下選取 [否]，以在虛擬機器上停用系統指派的身分識別，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

    > [!NOTE]
    > 不支援透過 Azure 入口網站將使用者指派的身分識別新增至虛擬機器。

   ![組態頁面螢幕擷取畫面](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>相關內容

- 如需受控服務識別的概觀，請參閱[概觀](overview.md)。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 入口網站，提供 Azure VM 的受控服務識別[存取權給另一個 Azure 資源](howto-assign-access-portal.md)。

