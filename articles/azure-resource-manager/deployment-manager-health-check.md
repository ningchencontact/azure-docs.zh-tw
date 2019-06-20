---
title: 導入健康情況整合首度發行至 Azure 部署管理員
description: 描述如何使用 Azure 部署管理員在許多區域中部署服務。 它會顯示安全部署實務，以便在推出到所有區域之前確認部署的穩定性。
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 4c2c38754b2051a4f61249f623f60f21e9690f5e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206617"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>導入健康情況整合首度發行至 Azure 部署管理員 （公開預覽）

[Azure Deployment Manager](./deployment-manager-overview.md)可讓您執行 Azure Resource Manager 資源的分段首度發行。 已排序的方式部署區域依區域資源。 整合的健康情況檢查的 「 Azure Deployment Manager 可以監視首度發行，而且會自動停止問題首度發行，以便您疑難排解並減少標尺的影響。 這個功能可降低迴歸中更新所造成的服務無法使用。

## <a name="health-monitoring-providers"></a>健全狀況監視提供者

為了讓健康情況整合盡量簡化，Microsoft 持續與一些最頂尖的服務健康情況監視公司合作，為您提供簡單的複製/貼上解決方案，以整合健康情況檢查與您的部署。 如果您還沒使用健全狀況監視，這些是絕佳的解決方案開始：

| ![azure 部署管理員健全狀況監視提供者 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理員健全狀況監視提供者 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理員健全狀況監視提供者 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog、 前置的監視和分析平台現代化的雲端環境。 請參閱[Datadog 會與 Azure Deployment Manager 的整合](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，-全方位私人和公用雲端服務的監視解決方案。 請參閱[Site24x7 如何整合與 Azure 部署管理員](https://www.site24x7.com/azure/adm.html)。| Wavefront、 多雲端應用程式環境的監視與分析平台。 請參閱[Wavefront 如何整合與 Azure 部署管理員](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>如何判斷服務健全狀況

[健全狀況監視提供者](#health-monitoring-providers)提供數種機制，來監視服務和警示的任何服務健康情況問題。 [Azure 監視器](../azure-monitor/overview.md)舉例說明一個這類的供應項目。 Azure 監視器可以用於超過特定閾值時建立警示。 比方說，您的記憶體和 CPU 使用量突然增加超過預期的層級時您將新的更新部署到您的服務。 當通知時，您可以採取修正動作。

這些健康狀態提供者通常會提供 REST Api，以便能以程式設計方式檢查您的服務監視的狀態。 使用簡單的狀況良好或狀況不良 signal （由 HTTP 回應碼表示），及/或收到訊號的詳細資訊與 REST Api 可以是回復運作。

新*healthCheck*步驟在 Azure 部署管理員可讓您宣告 HTTP 代碼，指出狀況良好的服務，或為更複雜的其餘部分結果，您甚至可以指定規則運算式，其中，如果兩者相符，表示狀況良好回應。

若要取得的 Azure 部署管理員健康情況檢查的安裝程式流程：

1. 建立健全狀況監視器透過您選擇的健全狀況服務提供者。
1. Azure Deployment Manager 首度發行過程中建立一或多個 healthCheck 步驟。 填妥 healthCheck 步驟，使用下列資訊：

    1. REST API 的健全狀況監視器 （依定義健全狀況服務提供者） 的 URI。
    1. 驗證資訊。 目前支援的 API 金鑰樣式驗證。
    1. [HTTP 狀態碼](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或規則運算式，定義狀況良好的回應。 請注意，您可能會提供規則運算式中，所有必須符合的回應才會被視為狀況良好，或者您可能會都提供其中任何必須符合才能被視為狀況良好回應的運算式。 支援兩種方法。

    下列 Json 是範例：

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. 在您的 Azure 部署管理員推出適當的時間來叫用 healthCheck 步驟。 在下列範例中，健全狀況檢查步驟中會叫用**postDeploymentSteps**的**stepGroup2**。

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

若要逐步解說範例，請參閱[教學課程：在 Azure 部署管理員中使用健康情況檢查](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>健康情況檢查的階段

此時 Azure Deployment Manager 知道如何查詢您的服務以及在什麼中若要這樣做首度發行階段的健全狀況。 不過，Azure Deployment Manager 也允許深度設定這些檢查的時機。 HealthCheck 步驟會執行 3 個連續的階段，全部都有可設定的持續時間： 

1. 等候

    1. 部署作業完成之後，可能會重新啟動 Vm，根據新的資料，或甚至是第一次啟動，重新設定。 它也會提供服務以開始發出到實用監視提供者的健全狀況彙總的健康情況訊號的時間。 在這個混亂的過程中，它可能沒有意義以檢查服務健康狀態，因為更新尚未到達穩定的狀態。 事實上，服務可能穩定狀況良好和狀況不良的狀態之間如定位資源。 
    1. 在等候階段中，不會監視服務健全狀況。 這用來允許已部署的資源之間開始健全狀況檢查程序之前的時間。 
1. Elastic

    1. 因為無法在所有情況下多久知道資源將移至試吃他們會趨於穩定，彈性的階段可讓彈性之間的時間週期的資源時可能不穩定之前，並在需要維持狀況良好的穩定時狀態。
    1. 彈性的階段開始時，Azure Deployment Manager 就會開始定期輪詢服務健全狀況提供的 REST 端點。 輪詢間隔是可設定的。 
    1. 如果健全狀況監視器恢復與訊號表示服務狀況不良，這些訊號會被忽略，彈性的階段會繼續，並會繼續輪詢。 
    1. 當健全狀況監視器恢復與指出服務狀況良好的訊號，彈性的階段結束，然後再 HealthyState 階段開始。 
    1. 因此，指定 「 彈性 」 階段的持續時間是時間的最大可以輪詢服務健全狀況之前狀況良好的回應會被視為必要項目所花費量。 
1. HealthyState

    1. 在 HealthyState 階段中，服務健康狀態持續輪詢一次彈性階段相同的間隔。 
    1. 服務預期可以維護整個指定的期間內的健康情況監視提供者從狀況良好的訊號。 
    1. 如果偵測到的狀況不良的回應在任何時間點 Azure Deployment Manager 將會停止整個首度發行，並傳回持有狀況不良的服務信號，REST 回應。
    1. 一旦 HealthyState 期間已經結束，healthCheck 完成，且部署會繼續下一個步驟。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何整合健康狀態監視在 Azure 部署管理員。 請前往下一篇文章，以了解如何使用部署管理員進行部署。

> [!div class="nextstepaction"]
> [教學課程： 整合健康狀態檢查在 Azure 部署管理員](./deployment-manager-tutorial-health-check.md)