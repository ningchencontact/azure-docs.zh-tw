---
title: EdgeAgent 和 EdgeHub 預期屬性參考 - Azure IoT Edge | Microsoft Docs
description: 檢閱 edgeAgent 和 edgeHub 模組對應項的特定屬性及其值
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190989"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性

IoT Edge 代理程式和 IoT Edge 中樞是構成 IoT Edge 執行階段的兩個模組。 如需模組各自執行哪些工作的相關資訊，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。 

本文提供執行階段模組對應項的所需屬性和報告屬性。 如需有關如何在 IoT Edge 裝置上部署模組的詳細資訊，請參閱 <<c0> [ 了解如何將模組部署，並在 IoT Edge 建立路由](module-composition.md)。

模組對應項包含： 

* **所需屬性**。 解決方案後端可以設定所需的屬性，以及模組可以讀取它們。 此模組也會收到變更通知的在所需的屬性。 所需的屬性會搭配報告屬性，以便同步處理模組的組態或狀況。

* **報告屬性**。 模組可以設定報告的屬性，以及解決方案後端可以讀取並加以查詢。 報告的屬性會搭配所需的屬性，以便同步處理模組的組態或狀況。 

## <a name="edgeagent-desired-properties"></a>EdgeAgent 的所需屬性

IoT Edge 代理程式的模組對應項稱為 `$edgeAgent`，並且會協調裝置與 IoT 中樞上執行之 IoT Edge 代理程式之間的通訊。 在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 

| 屬性 | 描述 | 必要項 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| runtime.type | 必須為「docker」 | 是 |
| runtime.settings.minDockerVersion | 此部署資訊清單需要設定為最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | stringified JSON，包含 IoT Edge 代理程式容器的記錄選項。 [Docker 記錄選項](https://docs.docker.com/engine/admin/logging/overview/) | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.username | 容器登錄的使用者名稱。 Azure Container Registry 的使用者名稱通常是登錄名稱。<br><br> 非公用的任何模組映像都需要登錄認證。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.password | 容器登錄的密碼。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.address | 容器登錄的位址。 Azure Container Registry 的位址通常是 {登錄名稱}.azurecr.io  。 | 否 |  
| systemModules.edgeAgent.type | 必須為「docker」 | 是 |
| systemModules.edgeAgent.settings.image | IoT Edge 代理程式映像的 URI。 目前，IoT Edge 代理程式無法自行更新。 | 是 |
| systemModules.edgeAgent.settings<br>.createOptions | stringified JSON，包含 IoT Edge 代理程式容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeAgent.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |
| systemModules.edgeHub.type | 必須為「docker」 | 是 |
| systemModules.edgeHub.status | 必須為「執行中」 | 是 |
| systemModules.edgeHub.restartPolicy | 必須為「永遠」 | 是 |
| systemModules.edgeHub.settings.image | IoT Edge 中樞映像的 URI。 | 是 |
| systemModules.edgeHub.settings<br>.createOptions | stringified JSON，包含 IoT Edge 中樞容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeHub.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |
| modules.{moduleId}.version | 使用者定義的字串，表示此模組的版本。 | 是 |
| modules.{moduleId}.type | 必須為「docker」 | 是 |
| modules.{moduleId}.status | {"running" \| "stopped"} | 是 |
| modules.{moduleId}.restartPolicy | {「 從未 」 \| 「 失敗 」 \| "在-unhealthy" \| 「 永遠 」} | 是 |
| modules.{moduleId}.settings.image | 模組映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | stringified JSON，包含模組容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| modules.{moduleId}.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |

## <a name="edgeagent-reported-properties"></a>EdgeAgent 的報告屬性

IoT Edge 代理程式報告屬性包含三個主要部分資訊：

1. 上次出現預期屬性之應用程式的狀態；
2. 目前在裝置上執行之模組的狀態；由 IoT Edge 代理程式報告；以及
3. 目前在裝置上執行之預期屬性的複本。

詳細資訊，一份目前所需的屬性，這個最後一項適合用來判斷裝置是否已套用最新的所需的屬性，或仍在執行先前的部署資訊清單。

> [!NOTE]
> 可以使用 [IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-query-language.md)查詢，以調查大規模部署的狀態時，IoT Edge 代理程式的報告屬性相當有用。 如需如何將 IoT Edge 代理程式屬性用於狀態的詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)。

下表不包含從預期屬性複製的資訊。

| 屬性 | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整數代表 IoT Edge 代理程式處理之所需屬性的最後版本。 |
| lastDesiredStatus.code | 此狀態碼是指看到 IoT Edge 代理程式的最後預期屬性。 允許的值：`200` 成功、`400` 無效的設定、`412` 無效的結構描述版本、`417` 預期屬性是空的、`500` 失敗 |
| lastDesiredStatus.description | 狀態的文字描述 |
| deviceHealth | 如果所有模組的執行階段狀態為 `running`、`stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 設定之所有模組的執行階段狀態為 `running` 或 `stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| runtime.platform.OS | 報告裝置上執行的作業系統 |
| runtime.platform.architecture | 報告裝置上的 CPU 架構 |
| systemModules.edgeAgent.runtimeStatus | IoT Edge 代理程式的報告狀態：{"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | IoT Edge 代理程式之報告狀態的文字描述。 |
| systemModules.edgeHub.runtimeStatus | IoT Edge 中樞的狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 狀況不良時，IoT Edge 中樞狀態的文字描述。 |
| systemModules.edgeHub.exitCode | 如果容器會結束，由 IoT Edge 中樞容器報告的結束代碼 |
| systemModules.edgeHub.startTimeUtc | IoT Edge 中樞的上次啟動時間 |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge 中樞的上次結束時間 |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge 中樞的上次重新啟動時間 |
| systemModules.edgeHub.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |
| modules.{moduleId}.runtimeStatus | 模組的狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 狀況不良時，模組狀態的文字描述。 |
| modules.{moduleId}.exitCode | 如果容器結束，模組容器報告的結束代碼 |
| modules.{moduleId}.startTimeUtc | 模組的上次啟動時間 |
| modules.{moduleId}.lastExitTimeUtc | 模組的上次結束時間 |
| modules.{moduleId}.lastRestartTimeUtc | 模組的上次重新啟動時間 |
| modules.{moduleId}.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |

## <a name="edgehub-desired-properties"></a>EdgeHub 的所需屬性

IoT Edge 中樞的模組對應項稱為 `$edgeHub`，並且會協調裝置與 IoT 中樞上執行之 IoT Edge 中樞之間的通訊。 在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 

| 屬性 | 描述 | 部署資訊清單中的必要項目 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| routes.{routeName} | 字串，表示 IoT Edge 中樞路由。 如需詳細資訊，請參閱 <<c0> [ 宣告路由](module-composition.md#declare-routes)。 | `routes` 元素可以存在但為空白。 |
| storeAndForwardConfiguration.timeToLiveSecs | 以秒為單位的時間，IoT Edge 中樞保留訊息是否與路由端點中斷連線的 IoT 中樞或本機模組。 值可以是任何正整數。 | 是 |

## <a name="edgehub-reported-properties"></a>EdgeHub 的報告屬性

| 屬性 | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整數代表 IoT Edge 中樞處理之所需屬性的最後版本。 |
| lastDesiredStatus.code | 狀態代碼，代表由 IoT Edge 中樞看到的最後預期屬性。 允許的值：`200` 成功、`400` 無效的設定、`500` 失敗 |
| lastDesiredStatus.description | 狀態文字描述。 |
| clients.{device or moduleId}.status | 此裝置或模組的連線狀態。 可能的值 {"connected" \| "disconnected"}。 只有模組身分識別可以處於中斷連線狀態。 連線到 IoT Edge 中樞的下游裝置只會在連線時顯示。 |
| clients.{device or moduleId}.lastConnectTime | 上一次裝置或模組連線。 |
| clients.{device or moduleId}.lastDisconnectTime | 上一次裝置或模組中斷連線。 |

## <a name="next-steps"></a>後續步驟

若要了解如何使用這些屬性建置部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。
