---
title: 在定型執行中使用秘密
titleSuffix: Azure Machine Learning
description: 使用工作區 Key Vault 以安全的方式將秘密傳遞至定型執行
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996579"
---
# <a name="use-secrets-in-training-runs"></a>在定型執行中使用秘密

在本文中，您將瞭解如何安全地在定型執行中使用秘密。 例如，若要連接到外部資料庫以查詢定型資料，您必須將使用者名稱和密碼傳遞給遠端執行內容。 以純文字將這類值編碼成定型腳本並不安全，因為它會公開秘密。 

相反地，您的 Azure Machine Learning 工作區已[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)為相關聯的資源。 此 Key Vault 可透過 Azure Machine Learning Python SDK 中的一組 Api，用來安全地將秘密傳遞至遠端執行

使用秘密的基本流程如下：
 1. 在本機電腦上，登入 Azure 並連接至您的工作區
 2. 在 [本機電腦] 上，于 [工作區] 中設定密碼 Key Vault
 3. 提交遠端執行。
 4. 在遠端執行中，從金鑰值取得秘密並加以使用。

## <a name="set-secrets"></a>設定秘密

在 Azure Machine Learning Python SDK 中， [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py)類別包含用來設定密碼的方法。 在您的本機 Python 會話中，先取得工作區 Key Vault 的參考，然後使用[set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-)方法，依名稱和值來設定密碼。

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

請勿將秘密值放在 Python 程式碼中，因為以純文字形式將它儲存在檔案中是不安全的。 相反地，從環境變數取得秘密值，例如 Azure DevOps 組建密碼，或從互動式使用者輸入。

您可以使用[list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-)方法列出秘密名稱。 如果名稱已存在，則__set_secret__方法會更新密碼值。

## <a name="get-secrets"></a>取得密碼

在您的本機程式碼中，您可以使用[Keyvault 取得 _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-)方法，依名稱取得密碼值。

在使用[實驗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)提交的回合中，使用[Run. get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-)方法。 由於提交的執行會感知其工作區，因此這個方法會將工作區具現化的快捷方式，並直接傳回秘密值。

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

請小心，不要藉由寫出或列印秘密值來公開。

Set 和 get 方法也具有批次版本[set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-)和[get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) ，可同時存取多個密碼。

## <a name="next-steps"></a>後續步驟

 * [View 範例筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [瞭解 Azure Machine Learning 的企業安全性](concept-enterprise-security.md)
