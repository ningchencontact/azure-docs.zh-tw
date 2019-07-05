---
title: 變更儲存體帳戶存取金鑰
titleSuffix: Azure Machine Learning service
description: 了解如何變更您的工作區所使用的 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 服務會使用 Azure 儲存體帳戶來儲存資料和模型。 當您重新產生儲存體帳戶的存取金鑰時，您必須更新 Azure Machine Learning 服務，才能使用新的金鑰。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543835"
---
# <a name="regenerate-storage-account-access-keys"></a>重新產生儲存體帳戶存取金鑰

了解如何變更 Azure Machine Learning 服務所使用的 Azure 儲存體帳戶的存取金鑰。 Azure Machine Learning 可以使用儲存體帳戶來儲存資料或定型的模型。

基於安全考量，您可能需要變更 Azure 儲存體帳戶的存取金鑰。 當您重新產生存取金鑰時，則必須更新 Azure Machine Learning，才能使用新的金鑰。 這兩個模型儲存體和資料存放區，azure Machine Learning 可能使用的儲存體帳戶。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 服務工作區。 如需詳細資訊，請參閱 <<c0> [ 建立工作區](setup-create-workspace.md)文章。

* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [Azure Machine Learning CLI 延伸模組](reference-azure-machine-learning-cli.md)。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>功能需要更新

（儲存記錄檔、 模型、 快照集等） 的 Azure 機器學習服務工作區以及為資料存放區，可以使用儲存體帳戶。 更新工作區的程序是單一的 Azure CLI 命令，並可更新的儲存體金鑰之後執行。 較為複雜，且需要探索哪些資料存放區目前正在使用的儲存體帳戶，然後重新註冊程序更新資料存放區。

> [!IMPORTANT]
> 更新工作區中使用 Azure CLI，並使用 Python，同時資料存放區。 更新只能有一個或另不敷使用，並可能會導致錯誤，直到同時更新。

若要探索您的資料存放區所使用的儲存體帳戶，請使用下列程式碼：

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

此程式碼會尋找任何已註冊資料存放區，使用 Azure 儲存體，並列出下列資訊：

* 資料存放區名稱：儲存體帳戶註冊過的資料存放區的名稱。
* 儲存體帳戶名稱︰Azure 儲存體帳戶的名稱。
* 容器：在此註冊會使用儲存體帳戶容器。

如果您計劃重新產生存取金鑰的儲存體帳戶存在的項目，將儲存的資料存放區名稱、 儲存體帳戶名稱和容器名稱。

## <a name="update-the-access-key"></a>更新存取金鑰

若要更新 Azure Machine Learning 服務來使用新的金鑰，請使用下列步驟：

> [!IMPORTANT]
> 執行所有步驟，更新兩種產品工作區中使用 CLI，並使用 Python 的資料存放區。 更新只能有一個或其他可能會造成錯誤，直到同時更新。

1. 重新產生金鑰。 如需有關重新產生存取金鑰，請參閱[管理儲存體帳戶](/azure/storage/common/storage-account-manage#access-keys)文章。 儲存新的金鑰。

1. 若要更新的工作區，以使用新的金鑰，請使用下列步驟：

    1. 若要登入 Azure 訂用帳戶，其中包含您的工作區，使用下列 Azure CLI 命令：

        ```azurecli-interactive
        az login
        ```

    1. 若要安裝 Azure Machine Learning 延伸模組，請使用下列命令：

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. 若要更新的工作區，以使用新的金鑰，請使用下列命令。 取代`myworkspace`與您的 Azure Machine Learning 工作區名稱，取代`myresourcegroup`包含該工作區的 Azure 資源群組的名稱。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        此命令會自動同步工作區所使用的 Azure 儲存體帳戶的新金鑰。

1. 若要重新註冊使用儲存體帳戶的資料，使用中的值[項目必須更新](#whattoupdate)區段，並以下列程式碼的步驟 1 中的索引鍵：

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    因為`overwrite=True`指定，此程式碼會覆寫現有的註冊，並更新為使用新的金鑰。

## <a name="next-steps"></a>後續步驟

如需有關如何註冊資料存放區的詳細資訊，請參閱 < [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別參考。
