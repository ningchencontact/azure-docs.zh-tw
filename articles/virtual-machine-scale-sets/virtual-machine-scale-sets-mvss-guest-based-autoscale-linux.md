---
title: "在 Linux 擴展集範本中搭配客體計量使用 Azure 自動調整規模 | Microsoft Docs"
description: "了解如何在 Linux 虛擬機器擴展集範本中使用客體計量自動調整規模"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>在 Linux 擴展集範本中使用客體計量自動調整規模

在 Azure 中蒐集自 VM 和擴展集的計量有兩種類型：部分來自主機 VM，其他則來自客體 VM。 概括而言，如果您使用標準的 CPU、磁碟及網路計量，則可能適合採用主機計量。 不過，如果您需要更大的計量選取範圍，則可能較適合採用客體計量。 讓我們看看兩者之間的差異：

主機計量比較簡單且更可靠。 它們不需要額外的安裝程式因為它們會收集由主機 VM，而客體度量需要您自行安裝[Windows Azure 診斷擴充功能](../virtual-machines/windows/extensions-diagnostics-template.md)或[Linux Azure 診斷擴充功能](../virtual-machines/linux/diagnostic-extension.md)的客體 VM 中。 使用客體計量而非主機計量的一個常見原因是，客體計量會提供比主機計量更大的計量選取範圍。 記憶體耗用量計量即為一例，這類計量只能透過客體計量使用。 [這裡](../monitoring-and-diagnostics/monitoring-supported-metrics.md)會列出支援的主機度量，而常用的客體計量則列於[這裡](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)。 本文將說明如何根據適用於 Linux 擴展集的客體計量來修改[最基本可行的擴展集範本](./virtual-machine-scale-sets-mvss-start.md)，以使用自動調整規模規則。

## <a name="change-the-template-definition"></a>變更範本定義

可行的最小小數位數組範本可以看到[這裡](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)，而範本部署 Linux 標尺來賓型自動調整設定，會看到[這裡](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json)。 讓我們逐步檢查用來建立此範本 (`git diff minimum-viable-scale-set existing-vnet`) 的差異：

首先，新增參數`storageAccountName`和`storageAccountSasToken`。 診斷代理程式度量將資料儲存在[資料表](../cosmos-db/table-storage-how-to-use-dotnet.md)此儲存體帳戶中。 從 Linux 診斷代理程式 3.0 版開始，不再支援使用儲存體存取金鑰。 請改用[SAS 權杖](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

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

接下來，修改該規模調整集合`extensionProfile`包含診斷延伸模組。 在此組態中，指定要用來儲存度量小數位數設定為收集度量，以及儲存體帳戶和 SAS 權杖的資源識別碼。 指定 （在此情況下，每隔一分鐘） 彙總度量資訊的頻率，以及要追蹤 （在這種情況下，百分比使用記憶體中） 的度量。 如需此設定及已使用記憶體的百分比以外之計量的詳細資訊，請參閱[這份文件](../virtual-machines/linux/diagnostic-extension.md)。

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

最後，會加入`autoscaleSettings`根據這些度量的資源來設定自動調整規模。 此資源含有 `dependsOn` 子句，其會參考擴展集以確定擴展集存在，然後再嘗試自動調整其規模。 如果您選擇其他度量，以自動調整規模上時，您會使用`counterSpecifier`從診斷延伸模組設定為`metricName`自動調整規模設定中。 如需自動調整規模設定的詳細資訊，請參閱[自動調整規模的最佳做法](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md)和 [Azure 監視器 REST API 參考文件](https://msdn.microsoft.com/library/azure/dn931928.aspx) \(英文\)。

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
