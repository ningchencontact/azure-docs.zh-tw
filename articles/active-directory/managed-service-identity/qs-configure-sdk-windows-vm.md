---
title: 如何使用 Azure SDK 設定已啟用受控服務識別的 Azure VM
description: 使用 Azure SDK 在 Azure VM 上設定及使用受控服務識別的逐步指示。
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257655"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>使用 Azure SDK 設定 VM 受控服務識別

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

受控服務識別在 Azure Active Directory (AD) 中為 Azure 服務提供自動管理的身分識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure SDK 來啟用和移除 Azure VM 的受控服務識別。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>支援受控服務識別的 Azure SDK 

Azure 透過一系列的 [Azure SDK](https://azure.microsoft.com/downloads) 來支援多個程式設計平台。 其中許多都已更新來支援受控服務識別，還提供對應的範例來示範用法。 這份清單會隨著新增額外的支援而更新：

| SDK | 範例 |
| --- | ------ | 
| .NET   | [從已啟用 MSI 的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [從已啟用 MSI 的虛擬機器管理儲存體](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [建立已啟用 MSI 的虛擬機器](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [建立具有 MSI 的 Azure VM](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>後續步驟

- 若要了解您還能如何使用 Azure 入口網站、PowerShell、CLI 和資源範本，請參閱「設定 Azure VM 的受控服務識別」底下的相關文章。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
