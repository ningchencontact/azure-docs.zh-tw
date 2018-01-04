---
title: "如何在 VM 上使用使用者指派受管理服務身分識別從 Azure Sdk"
description: "指派給使用者的 MSI 在 VM 上搭配使用 Azure Sdk 的程式碼範例。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f9a31a0500a6f5f1c49fc45d5811e28788e6f2b1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/22/2017
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>使用 Azure Sdk，與指派使用者給受管理服務身分識別 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]本文提供的 SDK 範例，示範如何使用指派給使用者的 MSI 其各自的 Azure SDK 支援的清單。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關啟用 VM 上的 MSI 的詳細資訊，請參閱[設定 VM 管理服務身分識別 (MSI) 使用 Azure CLI](msi-qs-configure-cli-windows-vm.md)，或其中一個變數 （使用 PowerShell、 Azure 入口網站、 範本或 Azure SDK） 的發行項。 

## <a name="sdk-code-samples"></a>SDK 程式碼範例

| SDK             | 程式碼範例 |
| --------------- | ----------- |
| Python          | [使用 MSI 直接從虛擬機器內部驗證](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [從已啟用 MSI 的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure SDK](https://azure.microsoft.com/downloads/)，以取得 Azure SDK 資源的完整清單，包括程式庫下載、文件及更多項目。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








