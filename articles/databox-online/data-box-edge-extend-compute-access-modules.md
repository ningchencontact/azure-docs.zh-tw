---
title: 管理 Azure 資料方塊邊緣存取模組來計算網路 |Microsoft Docs
description: 說明如何擴充計算網路，您的資料方塊邊緣，以存取透過外部 IP 的模組。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917231"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>啟用您的 Azure 資料方塊邊緣上的計算網路

本文說明如何在您的 Azure 資料方塊邊緣上執行的模組可以存取計算網路在裝置上啟用。

若要設定網路，您將採取下列步驟：

- 讓您計算的資料方塊的邊緣裝置上的網路介面
- 將模組加入計算網路存取您的資料方塊邊緣
- 確認模組可以存取已啟用的網路介面

在本教學課程中，您將使用 web 伺服器應用程式模組，以示範的案例。

## <a name="prerequisites"></a>必要條件

在開始之前，您將需要：

- 完成裝置設定與資料方塊的邊緣裝置。
- 您已完成**設定計算**依照步驟[教學課程：使用 Azure 資料方塊邊緣來轉換資料](data-box-edge-deploy-configure-compute-advanced.md#configure-compute)您裝置上。 您的裝置應該有相關聯的 IoT 中樞資源、 IoT 裝置和 IoT Edge 裝置。

## <a name="enable-network-interface-for-compute"></a>啟用適用於計算的網路介面

若要存取外部網路透過您的裝置上執行的模組，您必須將 IP 位址指派給您的裝置上的網路介面。 您可以從本機 Web UI 管理這些計算設定。

請在本機 Web UI 上採取下列步驟來設定計算的設定值。

1. 在本機 Web UI 中，移至 [組態] > [計算設定]  。  

2. **啟用**您想要用來連線至您將在裝置執行的計算模組的網路介面。

    - 如果使用靜態 IP 位址，請輸入網路介面的 IP 位址。
    - 如果使用 DHCP，會自動指派 IP 位址。 此範例會使用 DHCP。

    ![啟用計算設定 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. 選取 [套用]  來套用設定。 記下 指派給網路介面，如果使用 DHCP 的 IP 位址。

    ![啟用計算設定](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>新增 web 伺服器應用程式的模組

採取下列步驟來新增資料方塊的邊緣裝置上的 web 伺服器應用程式模組。

1. 移至您的資料方塊的邊緣裝置相關聯的 IoT 中樞資源，然後選取**IoT Edge 裝置**。
2. 選取資料方塊的邊緣裝置相關聯的 IoT Edge 裝置。 在 **裝置詳細資料**，選取**設定模組**。 在 **新增模組**，選取 **+ 新增**，然後選取**IoT Edge 模組**。
3. 在  **IoT Edge 自訂模組**刀鋒視窗中：

    1. 指定**名稱**針對您想要部署的 web 伺服器應用程式模組。
    2. 提供**映像 URI**模組映像。 擷取符合提供的名稱和標記的模組。 在此情況下，`nginx:stable`便會從公用提取 （已標記為穩定版） 的穩定的 nginx 映像[Docker 存放庫](https://hub.docker.com/_/nginx/)。
    3. 在 **容器建立選項**，貼上下列範例程式碼：  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        此設定可讓您存取模組透過計算網路的 IP *http* tcp 連接埠 8080 （與正在 80 的預設 web 伺服器連接埠）。

        ![在 IoT Edge 自訂模組 刀鋒視窗中指定連接埠資訊](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. 選取 [ **儲存**]。

## <a name="verify-module-access"></a>驗證模組的存取權

1. 請確認已成功部署模組，且正在執行。 在 **裝置詳細資料**頁面上**模組**索引標籤上，執行階段狀態模組的應該是**執行**。  
2. 連接到 web 伺服器應用程式的模組。 開啟瀏覽器視窗並輸入：

    `http://<compute-network-IP-address>:8080`

    您應該會看到正在執行的 web 伺服器應用程式。

    ![驗證模組的連線，透過指定的連接埠](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[透過 Azure 入口網站管理使用者](data-box-edge-manage-users.md)。
