---
title: "如何使用 Azure SDK 為 Azure 虛擬機器設定使用者指派的 MSI"
description: "逐步解說使用 Azure SDK 為 Azure 虛擬機器設定使用者指派的「受控服務識別 (MSI)」。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 097304162b85599acd1f4591091f986a646ebc2a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>使用 Azure SDK 為虛擬機器設定使用者指派的受控服務識別 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

在 Azure Active Directory 中，「受控服務識別」會提供受控身分識別給 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您將了解如何使用 Azure SDK 為 Azure 虛擬機器啟用和移除使用者指派的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK 搭配 MSI 支援 

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援 MSI，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| Python | [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [建立具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>後續步驟

- 若要了解如何在 Azure 虛擬機器上設定使用者指派的 MSI，請參閱「設定 Azure 虛擬機器的 MSI」底下的相關文章。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
