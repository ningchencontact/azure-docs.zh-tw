---
title: 支援的作業系統、容器引擎 - Azure IoT Edge | Microsoft Docs
description: 了解哪些作業系統可以執行 Azure IoT Edge 精靈和執行階段，以及針對您生產環境裝置支援的容器引擎
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892791"
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
![Moby 作為容器執行階段](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>作業系統
Azure IoT Edge 可以在能夠執行容器的大部分作業系統上執行，不過每個作業系統受支援的程度不盡相同。 以下將作業系統分組為各種階層，代表使用者可預期的支援程度。
* 第 1 層系統可以認定為正式支援。 這表示 Microsoft：
    * 在自動化的測試中有此作業系統
    * 有為它們提供安裝套件
* 第 2 層系統可以認定為與 Azure IoT Edge 相容，並且可以相對輕鬆地使用。 這表示：
    * Microsoft 已在平台上完成臨機操作測試，或者知道合作夥伴已在平台上成功執行過 Azure IoT Edge
    * 其他平台的安裝套件或許能在這些平台上運作
    
主機 OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 換句話說，您只能在 Linux 上使用 Linux 容器和在 Windows 上使用 Windows 容器。 使用 Windows 時，只支援處理程序隔離容器，不支援 Hyper-V 隔離容器。  

<br>
<center>
![主機 OS 符合客體 OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>第 1 層
正式推出
| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 否 | yes|
| Ubuntu Server 16.04 | yes | 否 |
| Ubuntu Server 18.04 | yes | 否 |

公開預覽版
| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core 組建 17763 | yes | 否 |
| Windows 10 IoT 企業版組建 17763 | yes | 否 |
| Windows Server 2019 | yes | 否 |

上述 Windows OS 是在 Windows 執行 Windows 容器之裝置的需求。 這是唯一支援的生產環境設定。 適用於 Windows 的 Azure IoT Edge 安裝程式套件可讓您在 Windows 上使用 Linux 容器，但僅適用於開發及測試。 生產環境不支援在 Windows 上使用 Linux 容器的設定。 任何版本的 Windows 10 組建 14393 或更新版本和 Windows Server 2016 或更新版本，都可以用於此開發案例。

### <a name="tier-2"></a>第 2 層

| 作業系統 | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | yes | yes |
| Debian 8 | yes | yes |
| Debian 9 | yes | yes |
| RHEL 7.5 | yes | yes |
| Ubuntu 18.04 | yes | yes |
| Ubuntu 16.04 | yes | yes |
| Wind River 8 | yes | 否 |
| Yocto | yes | 否 |


## <a name="virtual-machines"></a>虛擬機器
Azure IoT Edge 可以在虛擬機器中執行。 當客戶想要使用邊緣智慧增強現有基礎結構時，通常會採用此方法。 主機 VM OS 的系列必須一律與用於模組容器中之客體 OS 的系列相符。 當 Azure IoT Edge 直接在裝置上執行時，這會是相同的需求。 Azure IoT Edge 與基礎虛擬化技術無關，且可在 Hyper-V 和 vSphere 等平台所提供的 VM 中運作。

<br>
<center>
![VM 中的 Azure IoT Edge](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>最低系統需求
Azure IoT Edge 在小如 Raspberry Pi3 至伺服器等級的硬體上都能順利執行。 針對您的案例選擇正確的硬體會與您要執行的工作負載高度相關。 決定最終的裝置可能很複雜，不過您可以在傳統膝上型電腦或桌上型電腦上輕鬆開始建立原型解決方案。

在建立原型的同時進行體驗，有助於指引您選擇最終裝置。 您應該考慮的問題包括：您的工作負載包含多少模組、您模組的容器共用多少層、您的模組以哪個程式設計語言撰寫、您的模組會處理多少資料、您的模組是否需要任何加速其工作負載的特殊硬體、您解決方案所期望的效能特性為何、您的硬體預算為何？
