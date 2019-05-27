---
title: 包含檔案
description: 包含檔案
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151973"
---
1. 使用`dps-keygen`命令列公用程式產生連接字串：

    若要安裝[金鑰產生器公用程式](https://github.com/Azure/dps-keygen)，執行下列命令：

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. 若要產生連接字串，請執行下列命令使用您先前記下的連線詳細資料：

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. 複製的連接字串`dps-keygen`在您的裝置程式碼中使用的輸出。