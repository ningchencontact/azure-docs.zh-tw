---
title: "如何使用 Azure SDK 的 Azure vm 中設定指派給使用者的 MSI"
description: "步驟所設定的 Azure vm，使用 Azure SDK 的使用者指派受管理服務身分識別 (MSI) 的逐步指示。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>設定使用者指派受管理服務身分識別 (MSI) VM，使用 Azure SDK

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

受管理的服務身分識別提供 Azure 服務與 Azure Active Directory 中的受管理的身分識別。 您可以使用這個身分識別支援 Azure AD 驗證，而不需要在程式碼中的認證的服務驗證。 

在本文中，您會學習如何啟用及為 Azure VM 中，使用 Azure SDK 中移除指派給使用者的 MSI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK 搭配 MSI 支援 

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援 MSI，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| Python | [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [建立具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>後續步驟

- 請參閱底下 「 設定 MSI 的 Azure VM 」，若要了解如何設定指派給使用者的 MSI，以及在 Azure VM 上的相關文件。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
