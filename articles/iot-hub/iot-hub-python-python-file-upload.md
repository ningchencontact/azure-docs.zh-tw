---
title: 將裝置中的檔案上傳至使用 Python 的 Azure IoT 中樞 | Microsoft Docs
description: 如何使用適用於 Python 的 Azure IoT 裝置 SDK 將檔案從裝置上傳至雲端。 上傳的檔案會儲存在 Azure 儲存體 blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 37ea126952451eae81c548a11f70b9a258808d34
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147361"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>使用 IoT 中樞 (Python) 將檔案從裝置上傳至雲端

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本文說明如何使用 [IoT 中樞的檔案上傳功能](iot-hub-devguide-file-upload.md)，將檔案上傳到 [Azure blob 儲存體](../storage/index.yml)。 本教學課程說明如何：

* 安全地提供儲存體容器來上傳檔案。

* 使用 Python 用戶端透過 IoT 中樞上傳檔案。

將[遙測資料從裝置傳送至 IoT 中樞](quickstart-send-telemetry-python.md)快速入門會示範 IoT 中樞的基本裝置到雲端訊息功能。 不過，在某些情況下，您無法輕易地將裝置傳送的資料對應到 IoT 中樞接受且相對較小的裝置到雲端訊息。 當您需要從裝置上傳檔案時，您仍然可以使用安全可靠的 IoT 中樞。

> [!NOTE]
> IoT 中樞 Python SDK 目前僅支援上傳諸如 **.txt** 檔案等以字元為基礎的檔案。

在本教學課程結尾，您會執行 Python 主控台應用程式：

* **FileUpload.py**，其會使用 Python 裝置 SDK 將檔案上傳至儲存體。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>必要條件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>從裝置應用程式上傳檔案

在本節中，您可以建立裝置應用程式來將檔案上傳到 IoT 中樞。

1. 在命令提示字元上執行下列命令，以安裝 **azure-iothub-device-client** 套件：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. 使用文字編輯器來建立您會上傳至 Blob 儲存體的測試檔案。

    > [!NOTE]
    > IoT 中樞 Python SDK 目前僅支援上傳諸如 **.txt** 檔案等以字元為基礎的檔案。

3. 使用文字編輯器，在工作資料夾中建立 **FileUpload.py** 檔案。

4. 在 **FileUpload.py** 檔案開頭處新增下列 `import` 陳述式和變數。 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. 在您的檔案中，將 `[Device Connection String]` 更換為您 IoT 中樞裝置的連接字串。 將 `[Full path to file]` 更換為用來測試所建立檔案的路徑，或更換為您想要上傳的任何裝置檔案。 將 `[File name for storage]` 更換為您想要在檔案上傳至 Blob 儲存體之後，對檔案提供的名稱。 

6. 建立 **upload_blob** 函式的回呼：

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. 新增下列程式碼以連線用戶端並上傳檔案。 另包含 `main` 常式：

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. 儲存並關閉 **UploadFile.py** 檔案。

## <a name="run-the-application"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 在工作資料夾的命令提示字元中，執行下列命令：

    ```cmd/sh
    python FileUpload.py
    ```

2. 下列螢幕擷取畫面顯示 **FileUpload** 應用程式的輸出：

    ![simulated-device 應用程式的輸出](./media/iot-hub-python-python-file-upload/1.png)

3. 您可以使用入口網站檢視您所設定之儲存體容器中的上傳檔案：

    ![上傳的檔案](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已學到如何使用 IoT 中樞的檔案上傳功能來簡化從裝置上傳檔案。 您可以利用下列文章繼續探索 IoT 中樞功能和案例：

* [以程式設計方式建立 IoT 中樞](iot-hub-rm-template-powershell.md)

* [C SDK 簡介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
