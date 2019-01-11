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
ms.openlocfilehash: 6443260de0a8bd8531edb303fa581d281034fef3
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555603"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 支援的系統

有許多方式可尋求 Azure IoT Edge 產品的支援。

**報告 Bug** – Azure IoT Edge 產品大部分的開發都是在 IoT Edge 開放原始碼專案中進行。 您可以在專案的[問題頁面](https://github.com/azure/iotedge/issues)上報告錯誤。 我們也會快速地從專案中找出修正方式，並加入產品更新中。

**Microsoft 客戶支援小組** – 具有[支援方案](https://azure.microsoft.com/support/plans/)的任何使用者都可以接從 [Azure 入口網站](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)建立支援票證，與 Microsoft 客戶支援小組互動。

**功能要求** – Azure IoT Edge 產品會透過產品的 [User Voice 頁面](https://feedback.azure.com/forums/907045-azure-iot-edge)來追蹤功能要求。

## <a name="operating-systems"></a>作業系統
Azure IoT Edge 可以在能夠執行容器的大部分作業系統上執行，不過每個作業系統受支援的程度不盡相同。 以下將作業系統分組為各種階層，代表使用者可預期的支援程度。

### <a name="tier-1"></a>第 1 層
第 1 層系統可以認定為正式支援。 這表示 Microsoft：
* 在自動化的測試中有這些作業系統
* 有為它們提供安裝套件

正式推出
| 作業系統 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | 否 | 是|
| Ubuntu Server 16.04 | 是 | 否 |
| Ubuntu Server 18.04 | 是 | 否 |

公開預覽版
| 作業系統 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core 組建 17763 | 是 | 否 |
| 適用於 Windows 容器的 Windows 10 組建 17763<br><br>適用於 Linux 容器的 Windows 10 組建 14393 或更新版本\* | 是 | 否 |
| 適用於 Windows 容器的 Windows Server 2019<br><br>適用於 Linux 容器的 Windows Server 2016 或更新版本\* | 是 | 否 |

\* Microsoft 會在 Windows 裝置上提供適用於 Linux 容器的安裝套件，僅供開發及測試之用。 這不是用於實際執行環境的支援設定。 

### <a name="tier-2"></a>第 2 層
第 2 層系統可以認定為與 Azure IoT Edge 相容，並且可以相對輕鬆地使用。 這表示：
* Microsoft 已在平台上完成臨機操作測試，或者知道合作夥伴已在平台上成功執行過 Azure IoT Edge
* 其他平台的安裝套件或許能在這些平台上運作

| 作業系統 | AMD64 | ARM32 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | 是 | 是 |
| Debian 8 | 是 | 是 |
| Debian 9 | 是 | 是 |
| RHEL 7.5 | 是 | 是 |
| Ubuntu 18.04 | 是 | 是 |
| Ubuntu 16.04 | 是 | 是 |
| Wind River 8 | 是 | 否 |
| Yocto | 是 | 否 |

## <a name="container-engines"></a>容器引擎
無論是在哪個作業系統上執行，Azure IoT Edge 都需要容器引擎才能啟動模組。 Microsoft 提供了容器引擎 moby-engine，以滿足此需求。 它是基於 Moby 開放原始碼專案。 Docker CE 和 Docker EE 是其他常用的容器引擎。 它們也是基於 Moby 開放原始碼專案，並且與 Azure IoT Edge 相容。 Microsoft 盡最大努力為使用這些容器引擎的系統提供支援，不過，請恕 Microsoft 無法為系統內的問題提供修正。 基於這個理由，Microsoft 建議在生產系統上使用 moby-engine。

