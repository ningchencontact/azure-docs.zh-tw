---
title: 在 Windows 中保存狀態-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 Windows 中保存狀態
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992258"
---
# <a name="persist-state-in-windows"></a>在 Windows 中保存狀態

在 [事件方格] 模組中建立的主題和訂用帳戶預設會儲存在容器檔案系統中。 若無持續性，如果重新部署模組，則所有建立的中繼資料都會遺失。 若要在整個部署中保留資料，您必須將資料保存在容器檔案系統之外。 目前只有中繼資料會保存。 事件會儲存在記憶體中。 如果重新部署或重新開機事件方格模組，則任何未傳遞的事件都會遺失。

本文提供在 Windows 部署中部署具有持續性的事件方格模組所需的步驟。

> [!NOTE]
>事件方格模組會在 Windows 中以低許可權的使用者**ContainerUser**來執行。

## <a name="persistence-via-volume-mount"></a>透過磁片區掛接進行持續性

[Docker 磁片](https://docs.docker.com/storage/volumes/)區是用來保留跨部署的資料。 若要掛接磁片區，您需要使用 docker 命令來建立它、提供許可權，讓容器可以讀取、寫入，然後部署模組。 沒有任何布建可在 Windows 上自動建立具有必要許可權的磁片區。 您必須在部署之前先建立它。

1. 執行下列命令來建立磁片區：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 執行下列命令來取得磁片區對應的主機目錄

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   範例輸出：-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 將**使用者**群組新增至以**裝入**點指向的值，如下所示：
    1. 啟動 [檔案瀏覽器]。
    1. 流覽至 [**裝入**點] 所指向的資料夾。
    1. 以滑鼠右鍵按一下，然後選取 [**屬性**]。
    1. 選取 [安全性]。
    1. 在 [群組或使用者名稱] 底下，選取 [**編輯**]。
    1. 選取 [**新增**]，輸入 `Users`，選取 [**檢查名稱**]，然後選取 **[確定]** 。
    1. 在 [*使用者的許可權*] 底下，選取 [**修改**]，然後選取 **[確定]** 。
1. 使用 [系結] 來掛接此磁片**區，並**從 Azure 入口網站重新部署事件方格模組

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是**C：\\應用程式\\metadataDb**。


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>透過主機目錄掛接的持續性

或者，您也可以選擇在主機系統上建立目錄，並掛接該目錄。

1. 執行下列命令，在主機檔案系統上建立目錄。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用系結來掛接您的**目錄，並**從 Azure 入口網站重新部署事件方格模組。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >請勿變更 bind 值的第二個部分。 它會指向模組中的特定位置。 針對 windows 上的事件方格模組，它必須是**C：\\應用程式\\metadataDb**。

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
