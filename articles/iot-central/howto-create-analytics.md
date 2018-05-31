---
title: 為 Azure IoT 中心應用程式建立自訂分析 | Microsoft Docs
description: 了解如何以操作者的身分為 Azure IoT 中心應用程式建立自訂分析。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199930"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>如何使用分析來分析您的裝置資料

Microsoft Azure IoT 中心提供豐富的分析功能，讓來自您裝置的大量資料變得更有意義。 您可以使用分析功能，檢視及分析應用程式中[裝置集合](howto-use-device-sets.md)的資料。 裝置集合是使用者定義的裝置群組。 您可以將分析縮小至一小組裝置或單一裝置。

操作者可選擇左導覽功能表上的 [分析]，選擇裝置集合，然後選擇要在圖形上顯示的量值。 以下是您可用來進一步切割資料的工具：

* **量值：** 選擇要顯示的量值，例如溫度和溼度。
* **彙總：** 選擇量值的彙總函式。 例如，[最小值] 或 [平均]。
* **分割依據：** 依照裝置屬性或裝置名稱分割資料，以便向下切入。 例如，依照裝置位置分割：

     ![分析主頁面](media\howto-create-analytics\analytics-main.png)

* **時間範圍：** 您可以從其中一個預先定義的時間範圍選擇時間範圍內，或建立自訂時間範圍內：![分析時間範圍](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>變更視覺效果

您可以選擇下列其中一種模式來變更圖形，以符合您的視覺效果需求：

* **堆疊：** 每個量值的圖形都會堆疊起來，而且每個圖形都有自己的 Y 軸。 當您已選取多個量值，而且想要有這些量值的不同檢視時，堆疊圖形很有用。
* **未堆疊：** 每個量值的圖形都會根據一個 Y 軸繪製，但是 Y 軸的值會根據醒目提示的量值變更。 當您想要重疊多個量值，而且想要查看在相同的時間範圍內這些量值的模式，未堆疊的圖形很有用。
* **共用 Y 軸：** 所有圖形都會共用相同的 Y 軸，而該軸的值不會變更。 當您在利用分割依據來切割資料時，如果想要查看單一量值，共用 Y 軸圖形很有用。

## <a name="next-steps"></a>後續步驟

您現在已了解如何為 Azure IoT 中心應用程式建立自訂分析，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [準備及連線 Node.js 應用程式](howto-connect-nodejs.md)