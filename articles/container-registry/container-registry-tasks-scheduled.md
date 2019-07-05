---
title: Azure Container Registry 工作排程
description: 設定計時器，以定義的排程上執行的 Azure Container Registry 工作。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509699"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>定義的排程上執行的 ACR 工作

這篇文章會示範如何執行[ACR 工作](container-registry-tasks-overview.md)依排程。 藉由設定一或多個排程的工作*計時器觸發程序*。 

排程工作是適用於下列案例：

* 執行容器工作負載進行排程的維護作業。 例如，執行從登錄移除不必要的映像的容器化應用程式。
* 監視您即時網站一部分工作日期間，在生產環境映像上執行一組測試。

若要執行本文中的範例，您可以使用 Azure Cloud Shell 或 Azure CLI 的本機安裝。 如果您想要使用它在本機，2.0.68 版本或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。


## <a name="about-scheduling-a-task"></a>關於排程工作

* **觸發程序使用 cron 運算式**-工作的計時器觸發程序會使用*cron 運算式*。 運算式是指定分鐘、 小時、 天、 月和星期幾來觸發工作的五個欄位的字串。 支援最多每分鐘一次的頻率。 

  例如，運算式`"0 12 * * Mon-Fri"`中午 UTC 上每個工作日會觸發工作。 請參閱[詳細資料](#cron-expressions)本文稍後。
* **多重計時器觸發程序**-新增多個計時器工作允許，只要有不同的排程。 
    * 當您建立工作，或稍後再新增，請指定多個計時器觸發程序。
    * （選擇性） 命名的觸發程序，為了方便管理，或 ACR 工作將會提供預設觸發程序名稱。
    * 如果計時器排程重疊，一次，則 ACR 工作會觸發在排定的時間的工作對於每個計時器。 
* **其他工作觸發程序**-在計時器觸發的工作中，您也可以啟用觸發程序根據[來源的程式碼認可](container-registry-tutorial-build-task.md)或是[基底映像更新](container-registry-tutorial-base-image-update.md)。 像其他 ACR 的工作，您也可以[手動觸發][az-acr-task-run]排定的工作。

## <a name="create-a-task-with-a-timer-trigger"></a>使用計時器觸發程序建立工作

當您建立的工作[az acr 工作建立][az-acr-task-create]命令時，您可以選擇性地加入計時器觸發程序。 新增`--schedule`參數並傳遞計時器的 cron 運算式。 

簡單的範例，下列命令執行的觸發程序`hello-world`映像從 Docker Hub 21:00 UTC 在每天一次。 沒有來源的程式碼內容中執行工作。

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

執行[az acr 工作顯示][az-acr-task-show]命令來設定計時器觸發程序。 根據預設，基底映像的 update 觸發程序也會啟用。

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

觸發程序的工作以手動方式[az acr 工作執行][az-acr-task-run]以確保，它已正確設定：

```azurecli
az acr task run --name mytask --registry myregistry
```

如果容器順利執行，則輸出會是如下所示：

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

排程的時間之後, 執行[az acr 工作清單執行][az-acr-task-list-runs]命令，以確認計時器觸發的工作，如預期般運作：

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

成功的計時器時，輸出會是如下所示：

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>管理計時器觸發程序

使用[az acr 工作計時器][az-acr-task-timer]管理 ACR 工作的計時器觸發程序的命令。

### <a name="add-or-update-a-timer-trigger"></a>新增或更新的計時器觸發程序

建立工作之後，選擇性地使用將新增計時器觸發程序[az acr 工作計時器新增][az-acr-task-timer-add]命令。 下列範例會將計時器觸發程序名稱*timer2*要*mytask*先前所建立。 此計時器觸發的工作每天會在 10:30 UTC。

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

更新現有的觸發程序的排程，或變更其狀態、 使用[az acr 工作計時器更新][az-acr-task-timer-update]命令。 例如，更新名為觸發程序*timer2*觸發工作 11:30： 在 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>列出計時器觸發程序

[Az acr 工作計時器清單][az-acr-task-timer-list]命令會顯示工作設定的計時器觸發程序：

```azurecli
az acr task timer list --name mytask --registry myregistry
```

範例輸出︰

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>移除計時器觸發程序 

使用[az acr 工作計時器移除][az-acr-task-timer-remove]命令以移除工作中的計時器觸發程序。 下列範例會移除*timer2*從觸發*mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 運算式

使用 ACR 任務[NCronTab](https://github.com/atifaziz/NCrontab)解譯 cron 運算式的程式庫。 支援的運算式，ACR 工作中有五個以空格分隔的必要的欄位：

`{minute} {hour} {day} {month} {day-of-week}`

使用 cron 運算式的時區是國際標準時間 (UTC)。 時間為 24 小時制的小時。

> [!NOTE]
> ACR 工作不支援`{second}`或`{year}`cron 運算式中的欄位。 如果您複製 cron 運算式使用另一個系統中，務必移除這些欄位中，如果使用它們。

每個欄位可以具備下列類型的值：

|類型  |範例  |觸發時間  |
|---------|---------|---------|
|特定值 |<nobr>"5 * * * *"</nobr>|在 5 分鐘的整點過後每隔一小時|
|所有值 (`*`)|<nobr>"* 5 * * *"</nobr>|每分鐘的小時開頭 5:00 UTC （一天 60 次）|
|範圍 (`-` 運算子)|<nobr>"0 1-3 * * *"</nobr>|3 次每天，1:00、 2:00，與上午 3:00 UTC|  
|一組值 (`,` 運算子)|<nobr>"20,30,40 * * * *"</nobr>|每小時，在 20 分鐘、 30 分鐘及過去一小時 40 分鐘 3 次|
|間隔值 (`/` 運算子)|<nobr>"*/10 * * * *"</nobr>|每小時，在 10 分鐘的時間、 20 分鐘的時間等等，過去 1 小時 6 次

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 範例

|範例|觸發時間  |
|---------|---------|
|`"*/5 * * * *"`|每隔 5 分鐘一次|
|`"0 * * * *"`|每小時開始時一次|
|`"0 */2 * * *"`|每隔 2 小時一次|
|`"0 9-17 * * *"`|每小時一次從 9:00 為 17:00 UTC|
|`"30 9 * * *"`|上午 9:30 UTC 每一天|
|`"30 9 * * 1-5"`|上午 9:30 UTC 每個工作天|
|`"30 9 * Jan Mon"`|上午 9:30 年 1 月中每個星期一 UTC|


## <a name="next-steps"></a>後續步驟

來源的程式碼認可或基底映像更新觸發工作的範例，請參閱[ACR 工作的教學課程系列](container-registry-tutorial-quick-task.md)。



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli