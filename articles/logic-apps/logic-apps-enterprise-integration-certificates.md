---
title: 在 Azure Logic Apps 中使用憑證保護 B2B 訊息 | Microsoft Docs
description: 使用企業整合套件新增憑證以保護 B2B 訊息
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299329"
---
# <a name="secure-b2b-messages-with-certificates"></a>使用憑證保護 B2B 訊息

有時候您必須將 B2B 通訊列為機密。 為了幫助您保護企業整合應用程式 (特別是邏輯應用程式) 的 B2B 通訊，您可以將憑證新增至整合帳戶。 憑證是數位文件，可驗證電子通訊參與者的身分識別。
憑證可協助您以下列方式保護通訊：

* 將訊息內容加密
* 以數位方式簽署訊息  

您可以在企業整合應用程式中使用這些憑證：

* 公開憑證，必須先向憑證授權單位 (CA) 購買。
* 私人憑證，您可以自行核發。 這些憑證有時也稱為自我簽署憑證。

## <a name="upload-a-public-certificate"></a>上傳公開憑證

若要在具備 B2B 功能的 Logic Apps 中使用「公開憑證」，您必須先將憑證上傳到整合帳戶。 當您在自己建立的[合約](logic-apps-enterprise-integration-agreements.md)中定義屬性之後，就能使用憑證來幫助您保護 B2B 訊息。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在主要 Azure 功能表上，選取 [所有服務]。 在搜尋方塊中輸入「整合」，然後選取 [整合帳戶]。

   ![尋找您的整合帳戶](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. 在 [整合帳戶] 底下，選取要將憑證新增到其中的整合帳戶。

   ![選取要將憑證新增到的整合帳戶](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. 選擇 [憑證] 圖格。  

   ![選擇 [憑證]](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. 在 [憑證] 之下，選擇 [新增]。

   ![選擇 [新增]](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. 在 [新增憑證] 之下，提供憑證的詳細資料。
   
   1. 輸入您的憑證 [名稱]。 在憑證類型選取 [公開]。

   2. 選取 [憑證] 方塊右邊的資料夾圖示。 
   尋找和選取您想要上傳的憑證檔案。 
   完成後，請選擇 [確定]。

      ![上傳公開憑證](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure 會在驗證您的選取項目之後，將憑證上傳。

   ![查看新憑證](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>上傳私人憑證

若要在具備 B2B 功能的 Logic Apps 中使用「私人憑證」，您必須先將憑證上傳到整合帳戶。 您也必須要有先加入至 [Azure Key Vault](../key-vault/key-vault-get-started.md) 的私密金鑰。 

當您在自己建立的[合約](logic-apps-enterprise-integration-agreements.md)中定義屬性之後，就能使用憑證來幫助您保護 B2B 訊息。

> [!NOTE]
> 對於私人憑證，務必要新增對應的公開憑證，才會出現在 [AS2 合約](logic-apps-enterprise-integration-as2.md)的傳送和接收設定以用於簽署和加密訊息。

1. [將私密金鑰新增至 Key Vault](../key-vault/key-vault-get-started.md#add) 並提供 [金鑰名稱]。
   
2. 授權 Azure Logic Apps 以便對 Azure Key Vault 執行作業。 若要對 Logic Apps 服務主體授與存取權，請使用 PowerShell 命令 [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy)，例如：

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. 登入 [Azure 入口網站](https://portal.azure.com)。

4. 在主要 Azure 功能表上，選取 [所有服務]。 在搜尋方塊中輸入「整合」，然後選取 [整合帳戶]。

   ![尋找您的整合帳戶](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. 在 [整合帳戶] 底下，選取要將憑證新增到其中的整合帳戶。

6. 選擇 [憑證] 圖格。  

   ![選擇 [憑證] 圖格](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. 在 [憑證] 之下，選擇 [新增]。   

   ![選擇 [新增] 按鈕](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. 在 [新增憑證] 之下，提供憑證的詳細資料。
   
   1. 輸入您的憑證 [名稱]。 在憑證類型選取 [私人]。

   2. 選取 [憑證] 方塊右邊的資料夾圖示。 
   尋找和選取您想要上傳的憑證檔案。 
   此外，選取 [資源群組]、[金鑰保存庫] 以及 [金鑰名稱]。 
   完成時，選擇 [確定]。

      ![新增憑證](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure 會在驗證您的選取項目之後，將憑證上傳。

   ![查看新憑證](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>後續步驟

* [建立 B2B 合約](logic-apps-enterprise-integration-agreements.md)
