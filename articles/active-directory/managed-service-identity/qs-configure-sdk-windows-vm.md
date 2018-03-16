---
title: "如何使用 Azure SDK 設定已啟用 MSI 的 Azure VM"
description: "提供如何使用 Azure SDK 在 Azure VM 上設定及使用「受控服務識別 (MSI)」的逐步指示。"
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 42a238d0fda8d5ac87fbb23ab5c191452ef6e2be
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>使用 Azure SDK 設定虛擬機器受控服務識別 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

受控服務識別在 Azure Active Directory (AD) 中為 Azure 服務提供自動管理的身分識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure SDK 來啟用和移除 Azure VM 的 MSI。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Azure SDK 搭配 MSI 支援 

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援 MSI，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| .NET   | [從已啟用 MSI 的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [從已啟用 MSI 的虛擬機器管理儲存體](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [建立具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>後續步驟

- 若要了解您還可以如何使用 Azure 入口網站、PowerShell、CLI 和資源範本，請參閱「設定 Azure VM MSI」底下的相關文章。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
