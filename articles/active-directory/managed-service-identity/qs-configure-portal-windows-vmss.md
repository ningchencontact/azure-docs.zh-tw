---
title: 使用 Azure 入口網站在 Azure 虛擬機器擴展集上設定 MSI
description: 使用 Azure 入口網站在 Azure VMSS 上設定「受控服務身分識別 (MSI)」的逐步指示。
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 1ba9f827abeb0c0cf6430089e1fb504288550737
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900458"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 入口網站設定虛擬機器擴展集的「受控服務識別 (MSI)」

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure 入口網站為虛擬機器擴展集啟用和停用系統指派的識別。 目前不支援透過 Azure 入口網站在 Azure 虛擬機器擴展集中指派及移除使用者指派的識別。

> [!NOTE]
> 目前，不支援透過 Azure 入口網站執行使用者指派的身分識別作業。 請隨時回來查看是否有更新內容。

## <a name="prerequisites"></a>先決條件


- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>在 Azure 虛擬機器擴展集建立期間使用受控服務識別

目前，透過 Azure 入口網站建立虛擬機器不支援受控服務識別作業。 相反地，請參閱下列建立 Azure 虛擬機器擴展集的快速入門文件，先建立 Azure 虛擬機器擴展集：

- [在 Azure 入口網站建立虛擬機器擴展集](../../virtual-machine-scale-sets/quick-create-portal.md)  

然後進入下一節，取得在虛擬機器擴展集上啟用 MSI 的詳細資料。

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>在現有的 Azure VMMS 上啟用受控服務識別

若要在原本佈建時沒有使用系統指派身分識別的 VM 上啟用此身分識別：

1. 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽到需要的虛擬機器擴展集。

3. 藉由在 [受控服務識別] 下選取 [是]，以在虛擬機器上啟用系統指派的身分識別，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

   [![組態頁面螢幕擷取畫面](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集移除受控服務識別

如果您的虛擬機器擴展集不再需要 MSI：

1. 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 此外也請確定您的帳戶屬於在虛擬機器擴展集上具有寫入權限的角色。

2. 瀏覽到需要的虛擬機器擴展集。

3. 藉由在 [受控服務識別] 下選取 [否]，以在虛擬機器上停用系統指派的身分識別，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

   ![組態頁面螢幕擷取畫面](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>相關內容

- 如需受控服務識別的概觀，請參閱[概觀](overview.md)。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 入口網站，提供虛擬機器擴展集的 MSI [存取權給另一個 Azure 資源](howto-assign-access-portal.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
