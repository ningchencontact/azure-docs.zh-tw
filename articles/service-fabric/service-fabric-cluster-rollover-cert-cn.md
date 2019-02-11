---
title: 變換 Azure Service Fabric 叢集憑證 | Microsoft Docs
description: 了解如何變換以憑證通用名稱辨識的 Service Fabric 叢集憑證。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi
ms.openlocfilehash: 72640a4d917ddb2485199f0df1fead8b0bdcd1c9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55192960"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>手動變換 Service Fabric 叢集憑證
當 Service Fabric 叢集憑證即將到期時，您需要更新憑證。  只要叢集已[設定為使用基於通用名稱的憑證](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (而非指紋)，變換憑證將會是一件很簡單的事。  向憑證授權單位索取新到期日的新憑證。  自我簽署的憑證不支援生產 Service Fabric 叢集包含在 Azure 入口網站的叢集建立工作流程期間所產生的憑證。 新憑證的通用名稱必須與舊憑證相同。 

主機上安裝了多個驗證憑證時，Service Fabric 叢集會自動使用具有未來到期日的宣告憑證。 最佳做法是使用 Resource Manager 範本來佈建 Azure 資源。 在非生產環境中，可以使用以下指令碼將新憑證上傳到金鑰保存庫，然後將憑證安裝於虛擬機器擴展集： 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> 計算虛擬機器擴展集祕密不支援將相同的資源識別碼用於兩個不同的祕密，因為每個祕密都是已設定版本的唯一資源。 

若要深入了解，請閱讀以下文章：
* 了解[叢集安全性](service-fabric-cluster-security.md)。
* [更新及管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)

