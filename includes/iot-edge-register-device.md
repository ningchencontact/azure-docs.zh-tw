---
title: 包含檔案
description: 包含檔案
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bb0b140dd1f42cae1d5d4bb670af8780d66c1f80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768628"
---
建立模擬裝置的裝置身分識別，以便與 IoT 中樞通訊。 由於 IoT Edge 裝置的行為和管理方式不同於典型 IoT 裝置，您從開頭就要將此宣告為 IoT Edge 裝置。 

1. 在 Azure 入口網站中，瀏覽至您的 IoT 中樞。
1. 選取 [IoT Edge]，然後選取 [新增 IoT Edge 裝置]。

   ![加入 IoT Edge 裝置](./media/iot-edge-register-device/add-device.png)

1. 為您的模擬裝置提供唯一的裝置識別碼。
1. 選取 [儲存] 來新增您的裝置。
1. 從裝置清單中選取您的新裝置。
1. 複製 [連接字串 - 主要金鑰] 的值並儲存它。 您將在下一節中使用此值設定 IoT Edge 執行階段。 

