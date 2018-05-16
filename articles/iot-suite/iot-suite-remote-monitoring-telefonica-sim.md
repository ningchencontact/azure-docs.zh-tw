---
title: 在遠端監視解決方案中整合 SIM 資料 - Azure | Microsoft Docs
description: 本文說明如何將 Telefonica SIM 資料整合到遠端監視解決方案。
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>在遠端監視解決方案中整合 SIM 資料

## <a name="overview"></a>概觀
IoT 裝置通常會使用 SIM 卡連線到雲端，讓裝置能從任何地方傳送資料流。 Azure IoT 遠端監視解決方案能夠整合 SIM 管理資料，讓操作員也可透過 SIM 提供的資料追蹤裝置的健康情況。 遠端監視提供與 Telefonica IoT 的既有整合，讓客戶能夠使用它的 IoT 連線平台，將他們的裝置 SIM 連線資料同步處理到解決方案。 這個解決方案可透過 GitHub 存放庫進行擴充，以支援其他電信業者。
在本教學課程中，您了解如何：
* 將 SIM 資料整合到遠端監視解決方案
* 檢視即時遙測
* 檢視 SIM 資料 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT 整合設定

### <a name="prerequisites"></a>先決條件
若要將連線資料同步處理到 Azure 遠端監視解決方案，請遵循下列步驟：

1.  在 [Telefonica 網站](https://iot.telefonica.com/contact) \(英文\) 上填寫要求，然後選取 [Azure Remote Monitoring] \(Azure 遠端監視\)，其中包括您的連絡資料。
2.  Telefonica 將會啟動您的帳戶。 
3.  如果您還不是 Telefónica 用戶端，而想要享有這項服務或其他 IoT 雲端連線就緒服務，請瀏覽 [Telefonica 網站](https://iot.telefonica.com/contact) \(英文\)，然後選取 [Connectivity] \(連線能力\) 選項。

### <a name="telefonica-sim-setup"></a>Telefonica SIM 設定
Telefónica SIM & Azure 對應項裝置識別碼的關聯將會以 Telefónica IoT SIM「別名」屬性為根據。 

瀏覽至 [Telefónica IoT 連線平台入口網站](https://m2m-movistar-es.telefonica.com/) \(英文\) > [SIM Inventory] \(SIM 清查\) > 選取 SIM，然後使用您所需的對應項裝置識別碼來更新每個 SIM「別名」。 

此工作也可以在大量模式中完成 (請參閱 Telefónica IoT 連線平台使用者手冊)

![Telefonica 更新](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

若要將裝置連線到遠端監視，您可以使用 [C](iot-suite-connecting-devices-linux.md) 或 [Node](iot-suite-connecting-devices-node.md) 來遵循這些教學課程。 

## <a name="view-device-telemetry-and-sim-properties"></a>檢視裝置遙測和 SIM 屬性
一旦正確設定 Telefonica 帳戶且將裝置連線之後，您就能檢視裝置詳細資料和 SIM 資料。
您可以發佈下列連線參數：
* ICCID
* IP
* 網路目前狀態
* SIM 狀態
* 網路位置
* 已取用的資料流量

![儀表板](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>後續步驟

現在您已概略了解如何將 SIM 資料整合到 Azure IoT 遠端監視，以下是對於解決方案加速器所建議的後續步驟：

* [操作 Azure IoT 遠端監視解決方案](iot-suite-remote-monitoring-explore.md)
* [執行進階監視](iot-suite-remote-monitoring-monitor.md)
* [管理您的裝置](iot-suite-remote-monitoring-manage.md)
* [針對裝置問題進行疑難排解](iot-suite-remote-monitoring-maintain.md)

