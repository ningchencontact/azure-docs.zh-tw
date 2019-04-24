---
title: 在 Azure Cloud Shell 中使用 Azure 資源的受控識別 | Microsoft Docs
description: 在 Azure Cloud Shell 中使用 MSI 驗證程式碼
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: damaerte
ms.openlocfilehash: 7cadaaf67f9c6923ee9e9eb2596941aa8e1f0c9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60199477"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中使用 Azure 資源的受控識別

Azure Cloud Shell 可支援使用 Azure 資源的受控識別進行授權。 可利用此方式來擷取存取權杖，以安全地與 Azure 服務進行通訊。

## <a name="about-managed-identities-for-azure-resources"></a>關於適用於 Azure 資源的受控識別
建置雲端應用程式常見的難題是如何安全地管理程式碼中必須存在的認證，以向雲端服務進行驗證。 在 Cloud Shell 中，您可能需要驗證來自 Key Vault 的擷取，才能完成認證，而這可能需要指令碼。

Azure 資源的受控識別可在 Azure Active Directory (Azure AD) 中將受控識別自動提供給 Azure 服務，而降低解決此問題的難度。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

## <a name="acquire-access-token-in-cloud-shell"></a>在 Cloud Shell 中取得存取權杖

執行下列命令以將您的 MSI 存取權杖設定為環境變數 (`access_token`)。
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>處理權杖到期

本機 MSI 子系統會快取權杖。 因此，您可以視需要呼叫它，而只有在下列情況，線上呼叫 Azure AD 才會有結果：
- 由於快取中沒有權杖而發生快取遺漏
- 權杖已過期

如果您在程式碼中快取權杖，則應該妥善處理資源指出權杖過期的情節。

若要處理權杖錯誤，請造訪[以 CURL 取得 MSI 存取權杖的 MSI 頁面](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling)。

## <a name="next-steps"></a>後續步驟
[深入了解此 MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[從 MSI VM 取得存取權杖](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
