---
title: 為 Azure Stack 整合式系統部署產生 Azure Stack 公開金鑰基礎結構憑證 | Microsoft Docs
description: 說明 Azure Stack 整合式系統的 Azure Stack PKI 憑證部署程序。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 4a7432260167b8740567ad4308e102417abad82c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>產生適用於 Azure Stack 部署的 PKI 憑證
您現在已知道 Azure Stack 部署的 [PKI 憑證需求](azure-stack-pki-certs.md)，那麼您必須從您選擇的憑證授權單位 (CA) 取得這些憑證。 

## <a name="request-certificates-using-an-inf-file"></a>使用 INF 檔案要求憑證
使用 INF 檔案是向公用 CA 或內部 CA 要求憑證的方法之一。 如本節所述，Windows 內建 certreq.exe 公用程式可以使用 INF 檔案並指定憑證詳細資料，以產生要求檔案。 

### <a name="sample-inf-file"></a>INF 檔案範例 
憑證要求 INF 檔案範例可用來建立離線憑證要求檔案，以提交至 CA (內部或公用)。 INF 涵蓋單一萬用字元憑證中所有必要的端點 (包括選擇性 PaaS 服務)。 

範例 INF 檔案假設區域等於 **sea**，而外部 FQDN 值為 **sea&#46;contoso&#46;com**。為您的部署產生 .INF 檔案之前，請先變更這些值以符合您的環境。 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>產生要求並提交至 CA
下列工作流程說明如何自訂並使用稍早產生的範例 INF 檔案來向 CA 要求憑證：

1. **編輯並儲存 INF 檔案**。 複製所提供的範例，並將它儲存到新的文字檔。 以符合您部署的值取代主體名稱和外部 FQDN，然後將此檔案儲存為 .INF 檔案。
2. **使用 certreq 產生要求**。 使用 Windows 電腦，以系統管理員身分啟動命令提示字元，然後執行下列命令來產生要求 (.req) 檔案：`certreq -new <yourinffile>.inf <yourreqfilename>.req`。
3. **提交至 CA**。 將所產生的 .REQ 檔案提交至您的 CA (可以是內部或公用 CA)。
4. **匯入 .CER**。 CA 會傳回 .CER 檔案。 使用您產生要求檔案的相同 Windows 電腦，將傳回的 .CER 檔案匯入電腦/個人存放區。 
5. **匯出 .PFX 並複製到部署資料夾**。 將憑證 (包括私密金鑰) 匯出為 .PFX 檔案，然後將 .PFX 檔案複製到 [Azure Stack 部署 PKI 需求](azure-stack-pki-certs.md)中所述的部署資料夾。

## <a name="next-steps"></a>後續步驟
[準備 Azure Stack PKI 憑證](prepare-pki-certs.md)