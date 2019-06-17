---
title: 在 Linux 擴展集範本中搭配客體計量使用 Azure 自動調整規模 | Microsoft Docs
description: 了解如何在 Linux 虛擬機器擴展集範本中使用客體計量自動調整規模
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868981"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>在 Linux 擴展集範本中使用客體計量自動調整規模

有兩種廣泛的 Azure 中，收集自 Vm 和擴展集的計量：主機計量和客體計量。 概括而言，如果您想要使用標準的 CPU、 磁碟及網路計量，然後主機計量是不錯的選擇。 如果，不過，您需要更多選項的計量，應該會在研究過客體計量。

主機計量不需要額外的安裝程式因為它們會收集由主機 VM，而客體計量需要您安裝[Windows Azure 診斷擴充功能](../virtual-machines/windows/extensions-diagnostics-template.md)或[Linux Azure 診斷擴充功能](../virtual-machines/linux/diagnostic-extension.md)客體 VM 中。 使用客體計量而非主機計量的一個常見原因是，客體計量會提供比主機計量更大的計量選取範圍。 記憶體耗用量計量即為一例，這類計量只能透過客體計量使用。 [這裡](../azure-monitor/platform/metrics-supported.md)會列出支援的主機度量，而常用的客體計量則列於[這裡](../azure-monitor/platform/autoscale-common-metrics.md)。 這篇文章說明如何修改[基本的可行擴展集範本](virtual-machine-scale-sets-mvss-start.md)使用客體計量的 Linux 擴展集為基礎的自動調整規則。

## <a name="change-the-template-definition"></a>變更範本定義

在 [前一篇文章](virtual-machine-scale-sets-mvss-start.md)我們建立基本的擴展集範本。 現在，我們會使用較早的範本，並修改它建立範本部署 Linux 擴展集使用客體計量型自動調整。

首先，新增 `storageAccountName` 和 `storageAccountSasToken` 的參數。 診斷代理程式會將計量資料儲存於此儲存體帳戶的[表格](../cosmos-db/table-storage-how-to-use-dotnet.md)中。 從 Linux 診斷代理程式 3.0 版開始，不再支援使用儲存體存取金鑰。 請改用 [SAS 權杖](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

接下來，修改擴展集 `extensionProfile` 以包含診斷擴充功能。 在此設定中，指定要從中收集計量之擴展集的資源識別碼，以及要用來儲存計量的儲存體帳戶和 SAS 權杖。 指定彙總計量資訊的頻率 (在此案例中為每隔一分鐘)，以及要追蹤的計量 (在此案例中為已使用記憶體的百分比)。 如需此設定及已使用記憶體的百分比以外之計量的詳細資訊，請參閱[這份文件](../virtual-machines/linux/diagnostic-extension.md)。

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

最後，新增 `autoscaleSettings` 資源，以根據這些計量來設定自動調整規模。 此資源含有 `dependsOn` 子句，其會參考擴展集以確定擴展集存在，然後再嘗試自動調整其規模。 如果您選擇不同的計量來自動調整規模，可以使用來自診斷擴充功能設定的 `counterSpecifier`，作為自動調整規模設定中的 `metricName`。 如需自動調整規模設定的詳細資訊，請參閱[自動調整規模的最佳做法](../azure-monitor/platform/autoscale-best-practices.md)和 [Azure 監視器 REST API 參考文件](/rest/api/monitor/autoscalesettings) \(英文\)。

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
