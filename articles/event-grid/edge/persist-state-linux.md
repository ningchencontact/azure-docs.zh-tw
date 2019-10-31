---
title: 在 Linux 中保存狀態-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 Linux 中保存中繼資料
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100342"
---
# <a name="persist-state-in-linux"></a>在 Linux 中保存狀態

在 [事件方格] 模組中建立的主題和訂用帳戶預設會儲存在容器檔案系統中。 若無持續性，如果重新部署模組，則所有建立的中繼資料都會遺失。 目前只有中繼資料會保存。 事件會儲存在記憶體中。 如果重新部署或重新開機事件方格模組，則任何未傳遞的事件都會遺失。

本文提供在 Linux 部署中部署具有持續性之事件方格模組的步驟。

> [!NOTE]
>事件方格模組會以具有 UID `2000` 和名稱 `eventgriduser`的低許可權使用者身分執行。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接進行持續性

 [Docker 磁片](https://docs.docker.com/storage/volumes/)區是用來保留跨部署的資料。 您可以讓 docker 自動建立已命名的磁片區，作為部署事件方格模組的一部分。 此選項是最簡單的選項。 您可以在 [系結] 區段中指定要**建立的磁片**區名稱，如下所示：

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 Linux 上的事件方格模組，它必須是 **/app/metadataDb**。

例如，下列設定會導致建立會保存中繼資料的磁片區**egmetadataDbVol** 。

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

或者，您可以使用 docker 用戶端命令來建立 docker 磁片區。 

## <a name="persistence-via-host-directory-mount"></a>透過主機目錄掛接的持續性

除了 docker 磁片區以外，您也可以選擇掛接主機資料夾。

1. 首先，執行下列命令，在主機電腦上建立名為**eventgriduser**且識別碼為**2000**的使用者：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   md <your-directory-name-here>
   ```

    例如，執行下列命令將會建立名為**myhostdir**的目錄。

    ```sh
    md /myhostdir
    ```
1. 接下來，執行下列命令來建立此資料夾的**eventgriduser**擁有者。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用系結來掛接**目錄，並**從 Azure 入口網站重新部署事件方格模組。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    例如，

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >請勿變更 bind 值的第二個部分。 它會指向模組內的特定位置。 針對 linux 上的事件方格模組，它必須是 **/app/metadata**。
