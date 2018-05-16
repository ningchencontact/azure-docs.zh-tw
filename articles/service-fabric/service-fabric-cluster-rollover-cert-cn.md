---
title: 變換 Azure Service Fabric 叢集憑證 | Microsoft Docs
description: 了解如何變換以憑證通用名稱辨識的 Service Fabric 叢集憑證。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: a471e1aa8b68e8b94094dc79c8ae71f24cf290cb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>手動變換 Service Fabric 叢集憑證
當 Service Fabric 叢集憑證即將到期時，您需要更新憑證。  只要叢集已[設定為使用基於通用名稱的憑證](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (而非指紋)，變換憑證將會是一件很簡單的事。  向憑證授權單位索取新到期日的新憑證。  至於自我簽署憑證，包括在 Azure 入口網站中部署 Service Fabric 叢集時產生的憑證則不受支援。  新憑證的通用名稱必須與舊憑證相同。 

以下指令碼會將新憑證上傳到金鑰保存庫，然後將憑證安裝在虛擬機器規模集。  Service Fabric 叢集會自動使用具有最新到期日的憑證。

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

若要深入了解，請閱讀以下文章：
* 了解[叢集安全性](service-fabric-cluster-security.md)。
* [更新及管理叢集憑證](service-fabric-cluster-security-update-certs-azure.md)

