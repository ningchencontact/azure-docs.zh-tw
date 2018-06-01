---
title: 將 PrimaryNodeType 的 SKU 升級/移轉至更高 SKU 的程序 | Microsoft Docs
description: 了解如何使用 PowerShell 命令和 CLI 命令將 PrimaryNodeType 的 SKU 升級/移轉至更高的 SKU。
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
ms.locfileid: "34274021"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>將 PrimaryNodeType 的 SKU 升級/移轉至更高的 SKU

本文說明如何使用 Azure PowerShell 將 Service Fabric 叢集的主要節點類型的 SKU 升級/移轉至更高的 SKU

## <a name="add-a-new-virtual-machine-scale-set"></a>新增虛擬機器擴展集 

部署新的虛擬機器擴展集和 Load Balancer。 新的虛擬機器擴展集的 Service Fabric 延伸模組組態 (特別是節點類型) 應與您嘗試升級的舊擴展集相同。 請在 SF 總管中確認您的新節點是可用的。 

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell，以名為 *myResourceGroup* 的資源群組部署更新的 Resource Manager 範本 *template.json*：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

請參閱下列範例以修改 json 範本，並在現有的叢集中新增具有主要節點類型的虛擬機器擴展集資源

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>移除舊的虛擬機器擴展集

在打算要移除節點的情況下，停用舊有虛擬機器擴展集的 VM 執行個體。 此作業可能需要長時間才能完成。 您可以將所有節點一併停用，它們將會排入佇列中。 請靜候所有節點停用。 

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure Service Fabric PowerShell，從名為 *NTvm1* 的舊有虛擬機器擴展集停用名為 *NTvm1_0* 的節點執行個體：

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

移除完整的擴展集。 靜候擴展集佈建狀態變更為成功 

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell，從名為 *myResourceGroup* 的資源群組中移除名為 *NTvm1* 的完整擴展集：

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>移除舊有擴展集的相關 Load Balancer

移除舊有擴展集的相關 Load Balancer。 此步驟會使叢集停機一小段時間

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell，移除與資源群組 *myResourceGroup* 中的舊有擴展集相關的負載平衡器 *LB-myCluster-NTvm1*：

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>移除舊有擴展集的相關公用 IP

將舊有擴產集的相關公用 IP 位址的 DNS 設定儲存到變數中，然後移除該公用 IP 位址

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell 將公用 IP 位址 *LBIP-myCluster-NTvm1* 的 DNS 設定儲存到變數中，然後移除該 IP 位址：

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>更新與新的擴展集相關的公用 IP 位址

以舊有擴展集的相關公用 IP 位址更新與新的擴展集相關的公用 IP 位址的 DNS 設定

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure PowerShell，以在先前的步驟中儲存於變數中的 DNS 設定更新公用 IP 位址 *LBIP-myCluster-NTvm3* 的 DNS 設定：

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>從 FM 中移除 Service Fabric 節點的知識

通知 Service Fabric 關閉的節點已從叢集中移除。 (請為舊有虛擬機器擴展集的所有 VM 執行個體執行此命令) (如果舊有虛擬機器擴展集的耐久性層級為銀級或金級，則可能不需要執行此步驟。 因為系統會自動完成該步驟。)

### <a name="azure-powershell"></a>Azure PowerShell
下列範例會使用 Azure Service Fabric PowerShell 通知 Service Fabric 名為 *NTvm1_0* 的節點已移除：

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```