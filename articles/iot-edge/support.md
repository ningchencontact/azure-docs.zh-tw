---
title: 支援的作業系統、容器引擎 - Azure IoT Edge | Microsoft Docs
description: 了解哪些作業系統可以執行 Azure IoT Edge 精靈和執行階段，以及針對您生產環境裝置支援的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5bc133e81f9917aafb406a6bfb27922cdba48ef5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190000"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支援的系統

有許多方式可尋求 Azure IoT Edge 產品的支援。

**報告 Bug** – Azure IoT Edge 產品大部分的開發都是在 IoT Edge 開放原始碼專案中進行。 您可以在專案的[問題頁面](https://github.com/azure/iotedge/issues)上報告錯誤。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

**Microsoft 客戶支援小組** – 具有[支援方案](https://azure.microsoft.com/support/plans/)的任何使用者都可以接從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)建立支援票證，與 Microsoft 客戶支援小組互動。

**功能要求** – Azure IoT Edge 產品會透過產品的 [User Voice 頁面](https://feedback.azure.com/forums/907045-azure-iot-edge)來追蹤功能要求。

## <a name="container-engines"></a>容器引擎
Azure IoT Edge 需要容器引擎來啟動模組，因為它們是實作為容器。 Microsoft 提供了容器引擎 moby-engine，以滿足此需求。 它是基於 Moby 開放原始碼專案。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它們也是基於 Moby 開放原始碼專案，並且與 Azure IoT Edge 相容。 Microsoft 盡最大努力為使用這些容器引擎的系統提供支援，不過，請恕 Microsoft 無法為系統內的問題提供修正。 基於這個理由，Microsoft 建議在生產系統上使用 moby-engine。

<br>
<center>

![為容器執行階段的白鯨](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>作業系統
大多數容器; 可執行的作業系統上執行的 azure IoT Edge不過，所有這些系統不同樣支援。 以下將作業系統分組為各種階層，代表使用者可預期的支援程度。
* 第 1 層系統可以認定為正式支援。 適用於第 1 層系統，Microsoft:
    * 在自動化的測試中有此作業系統
    * 有為它們提供安裝套件
* 第 2 層系統可以認定為與 Azure IoT Edge 相容，並且可以相對輕鬆地使用。 適用於第 2 層系統：
    * Microsoft 平台上的臨機操作測試完成或知道的夥伴，成功執行平台上的 Azure IoT Edge
    * 其他平台的安裝套件或許能在這些平台上運作
    
主機 OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 換句話說，您只能在 Linux 上使用 Linux 容器和在 Windows 上使用 Windows 容器。 使用 Windows，唯一支援的隔離的容器的程序時不 HYPER-V 隔離容器。  

<br>
<center>

![主機作業系統符合客體 OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 層
正式推出

| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 否 | 是|
| Ubuntu Server 16.04 | 是 | 否 |
| Ubuntu Server 18.04 | 是 | 否 |

公開預覽版

| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT 核心版，建置 17763 | 是 | 否 |
| Windows 10 IoT 企業版，建置 17763 | 是 | 否 |
| Windows Server 2019 | 是 | 否 |

以上所列之 Windows 作業系統是在 Windows 執行 Windows 容器的裝置的需求。 此設定是唯一支援的組態用於生產環境。 Windows 的 Azure IoT Edge 安裝套件可讓使用 Windows; 上的 Linux 容器不過，這項設定適用於開發和僅限測試。 生產環境不支援在 Windows 上使用 Linux 容器的設定。 任何版本的 Windows 10 組建 14393 或更新版本和 Windows Server 2016 或更新版本，都可以用於此開發案例。

### <a name="tier-2"></a>第 2 層

| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | 是 | 是 |
| Debian 8 | 是 | 是 |
| Debian 9 | 是 | 是 |
| RHEL 7.5 | 是 | 是 |
| Ubuntu 18.04 | 是 | 是 |
| Ubuntu 16.04 | 是 | 是 |
| Wind River 8 | 是 | 否 |
| Yocto | 是 | 否 |


## <a name="virtual-machines"></a>虛擬機器
Azure IoT Edge 可以在虛擬機器中執行。 使用虛擬機器為 IoT Edge 裝置時，一般客戶想要運用邊緣智慧強化現有的基礎結構。 主機 VM OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 Azure IoT Edge 裝置上直接執行時，這項需求是與相同。 Azure IoT Edge 與基礎虛擬化技術無關，且可在 Hyper-V 和 vSphere 等平台所提供的 VM 中運作。

<br>
<center>

![在 VM 中的 azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最低系統需求
Azure IoT Edge 在小如 Raspberry Pi3 至伺服器等級的硬體上都能順利執行。 選擇正確的硬體，您的案例取決於您想要執行的工作負載。 決定最終的裝置可能很複雜，不過您可以在傳統膝上型電腦或桌上型電腦上輕鬆開始建立原型解決方案。

在建立原型的同時進行體驗，有助於指引您選擇最終裝置。 您應該考慮的問題包括： 

* 您的工作負載中，有多少個模組？
* 您的模組容器共用多少層級？
* 在何種語言寫入您的模組？ 
* 資料量將您的模組會處理？
* 您的模組，以加速其工作負載需要任何特殊的硬體嗎？
* 您的解決方案所需的效能特性有哪些？
* 您的硬體預算是什麼？
