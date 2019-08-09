---
title: 變更儲存體帳戶存取金鑰
titleSuffix: Azure Machine Learning service
description: 瞭解如何變更工作區所使用之 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 服務會使用 Azure 儲存體帳戶來儲存資料和模型。 當您重新產生儲存體帳戶的存取金鑰時, 您必須將 Azure Machine Learning 服務更新為使用新的金鑰。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: b23d54cb74bea7eaaea5f55bd491bed40381ee2b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848174"
---
# <a name="regenerate-storage-account-access-keys"></a>重新產生儲存體帳戶存取金鑰

瞭解如何變更 Azure Machine Learning 服務所使用之 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 可以使用儲存體帳戶來儲存資料或定型模型。

基於安全考慮, 您可能需要變更 Azure 儲存體帳戶的存取金鑰。 當您重新產生存取金鑰時, 必須將 Azure Machine Learning 更新為使用新的金鑰。 Azure Machine Learning 可能會同時針對模型儲存體和資料存放區使用儲存體帳戶。

## <a name="prerequisites"></a>先決條件

* Azure Machine Learning 服務工作區。 如需詳細資訊, 請參閱[建立工作區](how-to-manage-workspace.md)文章。

* [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [AZURE MACHINE LEARNING CLI 擴充](reference-azure-machine-learning-cli.md)功能。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新的內容

儲存體帳戶可供 Azure Machine Learning 服務工作區使用 (將記錄、模型、快照集等) 和資料存放區。 更新工作區的程式是單一 Azure CLI 命令, 而且可以在更新儲存體金鑰後執行。 更新資料存放區的程式較牽涉, 而且需要探索哪些資料存放區目前正在使用儲存體帳戶, 然後再重新註冊。

> [!IMPORTANT]
> 使用 Azure CLI 和使用 Python 的資料存放區同時更新工作區。 僅更新其中一個或另一個不足夠, 而且可能會導致錯誤, 直到兩者都更新為止。

若要探索您的資料存放區所使用的儲存體帳戶, 請使用下列程式碼:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

此程式碼會尋找使用 Azure 儲存體的任何已註冊資料存放區, 並列出下列資訊:

* 資料存放區名稱:儲存體帳戶註冊所在的資料存放區名稱。
* 儲存體帳戶名稱︰Azure 儲存體帳戶的名稱。
* 箱此註冊所使用之儲存體帳戶中的容器。

如果您計畫在重新產生存取金鑰的儲存體帳戶中存在專案, 請儲存資料存放區名稱、儲存體帳戶名稱和容器名稱。

## <a name="update-the-access-key"></a>更新存取金鑰

若要更新 Azure Machine Learning 服務以使用新的金鑰, 請使用下列步驟:

> [!IMPORTANT]
> 執行所有步驟, 使用 CLI 來更新工作區, 並使用 Python 資料存放區。 只更新其中一個或其他可能會導致錯誤, 直到兩者都更新為止。

1. 重新產生金鑰。 如需重新產生存取金鑰的詳細資訊, 請參閱[管理儲存體帳戶](/azure/storage/common/storage-account-manage#access-keys)一文。 儲存新的金鑰。

1. 若要更新工作區以使用新的金鑰, 請使用下列步驟:

    1. 若要使用下列 Azure CLI 命令登入包含您工作區的 Azure 訂用帳戶:

        ```azurecli-interactive
        az login
        ```

    1. 若要安裝 Azure Machine Learning 擴充功能, 請使用下列命令:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. 若要更新工作區以使用新的金鑰, 請使用下列命令。 將`myworkspace`取代為您的 Azure Machine Learning 工作區名稱`myresourcegroup` , 並將取代為包含工作區的 Azure 資源組名。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        此命令會自動為工作區所使用的 Azure 儲存體帳戶同步處理新的金鑰。

1. 若要重新註冊使用儲存體帳戶的資料存放區, 請使用 [[需要更新的內容](#whattoupdate)] 區段中的值, 以及步驟1中包含下列程式碼的金鑰:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    由於`overwrite=True`指定了, 此程式碼會覆寫現有的註冊, 並將其更新為使用新的金鑰。

## <a name="next-steps"></a>後續步驟

如需註冊資料存放區的詳細資訊, 請[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)參閱類別參考。
