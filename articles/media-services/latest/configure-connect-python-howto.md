---
title: 連線到 Azure 媒體服務 v3 API-Python
description: 了解如何連接到媒體服務 v3 API 與 Python。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733091"
---
# <a name="connect-to-media-services-v3-api---python"></a>連線到媒體服務 v3 API-Python

這篇文章說明如何連接到 Azure 媒體服務 v3 Python SDK 中使用服務主體的登入方法。

## <a name="prerequisites"></a>必要條件

- 下載從 Python [python.org](https://www.python.org/downloads/)
- 請務必設定`PATH`環境變數
- [建立媒體服務帳戶](create-account-cli-how-to.md)。 請務必記住的資源群組名稱和媒體服務帳戶名稱。
- 請依照下列中的步驟[存取 Api](access-api-cli-how-to.md)主題。 記錄的訂用帳戶識別碼、 應用程式識別碼 （用戶端識別碼）、 驗證金鑰 （密碼） 和租用戶識別碼，您需要在稍後的步驟。

## <a name="install-the-modules"></a>安裝模組

若要搭配使用 Python 的 Azure 媒體服務，您需要安裝這些模組。

* `azure-mgmt-resource`模組，其中包含適用於 Active Directory 的 Azure 模組。
* `azure-mgmt-media`模組，其中包含媒體服務實體。

開啟命令列工具，並使用下列命令來安裝的模組。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>連線至 Python 用戶端

1. 建立具有檔案`.py`延伸模組
1. 您最愛的編輯器中開啟檔案
1. 加入檔案之後的程式碼。 程式碼會匯入所需的模組，並建立您要連線到媒體服務的 Active Directory 認證物件。

      所得的值設定變數的值[存取 Api](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. 執行檔案

## <a name="next-steps"></a>後續步驟

- 使用 [Python SDK](https://aka.ms/ams-v3-python-sdk)。
- 檢閱媒體服務 [Python 參考](https://aka.ms/ams-v3-python-ref)文件。
