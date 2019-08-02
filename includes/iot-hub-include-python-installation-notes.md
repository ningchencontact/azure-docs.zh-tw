---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640443"
---
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

* **Windows**

    * [Python 2.x 或](https://www.python.org/downloads/)3.x。 請務必使用安裝程式所需的 32 位元或 64 位元安裝。 在安裝期間出現系統提示時，務必將 Python 新增至平台特有的環境變數。 如果您是使用 Python 2.x，可能需要[安裝或升級 pip (Python 套件管理系統](https://pip.pypa.io/en/stable/installing/))。

    * 如果您使用 Windows 作業系統, 請確定您有正確版本的[Visual C++ ](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)可轉散發套件, 以允許從 Python 使用原生 dll。 我們建議使用最新版本。

    * 如有需要, 請使用命令來安裝[azure iothub-裝置用戶端](https://pypi.org/project/azure-iothub-device-client/)套件`pip install azure-iothub-device-client`

    * 如有需要, 請使用命令來安裝[azure iothub-服務用戶端](https://pypi.org/project/azure-iothub-service-client/)套件`pip install azure-iothub-service-client`

* **Mac OS**

    針對 Mac OS, 您需要 Python 3.7.0 (或 2.7) + libboost-1.67 + 捲曲 7.61.1 (全部都是透過 homebrew 安裝)。 任何其他散發/OS 可能會內嵌不同版本的 & 相依性, 而這些相依性將無法使用, 而且會在執行時間產生 ImportError。

    `azure-iothub-service-client` 和 `azure-iothub-device-client` 的 pip 套件目前僅適用於 Windows OS。 針對 Linux/Mac OS, 請參閱[準備適用于 Python 的開發環境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)文章的 Linux 和 Mac os 特定章節。

> [!NOTE]
> 在範例中匯入 iothub_client 時, 有數個錯誤報表。 如需有關處理**ImportError**問題的詳細資訊, 請參閱[處理 ImportError 問題](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)。
