---
title: 使用 Azure 入口網站在 Azure 虛擬機器擴展集上設定 MSI
description: 使用 Azure 入口網站在 Azure VMSS 上設定「受控服務身分識別 (MSI)」的逐步指示。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: d9b493203a78aebdfadef15cf53d9cc023bb66f8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 入口網站設定 Azure 虛擬機器擴展集的受控服務身分識別 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure 入口網站啟用和移除 Azure 虛擬機器擴展集的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>在 Azure 虛擬機器擴展集建立期間啟用 MSI

從本文編寫完成後，不支援在 Azure 入口網站中建立虛擬機器擴展集期間啟用 MSI。 相反地，請參閱下列建立 Azure 虛擬機器擴展集的快速入門文件，先建立 Azure 虛擬機器擴展集：

- [在 Azure 入口網站建立虛擬機器擴展集](../../virtual-machine-scale-sets/quick-create-portal.md)  

然後進入下一節，取得在虛擬機器擴展集上啟用 MSI 的詳細資料。

## <a name="enable-msi-on-an-existing-azure-vmms"></a>在現有的 Azure VMMS 上啟用 MSI

如果您的虛擬機器擴展集原本沒有佈建MSI：

1. 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽到需要的虛擬機器擴展集。

3. 按一下 [組態] 頁面，並選取「受控服務身分識別」下的 [是] 來啟用虛擬機器擴展集上的 MSI，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

   ![組態頁面螢幕擷取畫面](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>從 Azure 虛擬機器擴展集中移除 MSI

如果您的虛擬機器擴展集不再需要 MSI：

1. 使用與包含虛擬機器擴展集的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 此外也請確定您的帳戶屬於在虛擬機器擴展集上具有寫入權限的角色。

2. 瀏覽到需要的虛擬機器擴展集。

3. 按一下 [組態] 頁面，並選取 [受控服務身分識別] 下的 [否] 來移除虛擬機器擴展集上的 MSI，然後按一下 [儲存]。 此作業可能需要 60 秒以上才能完成：

   ![組態頁面螢幕擷取畫面](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>後續步驟

- 如需 MSI 的概觀，請參閱[受控服務識別概觀](overview.md)。
- 使用 Azure 入口網站，提供虛擬機器擴展集的 MSI [存取權給另一個 Azure 資源](howto-assign-access-portal.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
