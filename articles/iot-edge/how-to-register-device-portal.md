---
title: 從 Azure 入口網站註冊新的裝置 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 入口網站來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495348"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>從 Azure 入口網站註冊新的 Azure IoT Edge 裝置

您必須先向 IoT 中樞註冊 IoT 裝置，才能將 IoT 裝置與 Azure IoT Edge 搭配使用。 一旦您註冊裝置時，您會收到可用來設定您的裝置，IoT Edge 的工作負載的連接字串。

本文說明如何使用 Azure 入口網站註冊新的 IoT Edge 裝置。

## <a name="prerequisites"></a>必要條件

可用的標準[IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)您 Azure 訂用帳戶中。

## <a name="create-a-device"></a>建立裝置

在 Azure 入口網站中，我們將在連線至 IoT 中樞，但未啟用 Edge 的裝置上分開建立與管理 IoT Edge 裝置。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
2. 選取功能表中的 [IoT Edge]  。
3. 選取 [新增 IoT Edge 裝置]  。
4. 提供描述性裝置識別碼。 使用預設設定，自動產生驗證金鑰，並將新的裝置連接到您的中樞。
5. 選取 [ **儲存**]。

## <a name="view-all-devices"></a>檢視所有裝置

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge]  頁面。

## <a name="retrieve-the-connection-string"></a>擷取連接字串

當您準備好開始設定裝置時，需要連接字串才能利用實體裝置在 IoT 中樞中的身分識別來連結實體裝置。

1. 從**IoT Edge**在入口網站頁面上，按一下從清單中的 IoT Edge 裝置的裝置識別碼。
2. 複製 [連接字串 (主要金鑰)]  或 [連接字串 (次要金鑰)]  的值。

## <a name="next-steps"></a>後續步驟

了解如何[將模組部署到裝置，以使用 Azure 入口網站](how-to-deploy-modules-portal.md)。
