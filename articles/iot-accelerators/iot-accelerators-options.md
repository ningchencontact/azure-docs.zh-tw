---
title: Microsoft Azure IoT 選項 | Microsoft Docs
description: 選擇如何使用 Azure IoT 解決方案加速器、Azure IoT 中心或 Azure IoT 中樞實作 IoT 解決方案。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 56a19e9cbeb6e7d30171d23ff918a34ba423d2f2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725576"
---
# <a name="compare-azure-iot-options"></a>比較各個 Azure IoT 選項

[Azure 和物聯網](iot-accelerators-what-is-azure-iot.md)一文會說明典型 IoT 解決方案架構與下列層級：

* 裝置連線能力和管理
* 資料處理和分析
* 簡報及商務整合

若要實作此架構，Azure IoT 會提供數個選項，每個選項都適用於不同客戶需求：

* [Azure IoT 解決方案加速器](../active-directory-domain-services/index.yml)是[解決方案加速器](iot-accelerators-what-are-solution-accelerators.md)的企業級集合，建置於 Azure 平台即服務 (PaaS) 上，可讓您加速開發自訂的 IoT 解決方案。

* [Azure IoT 中心](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions)是軟體即服務 (SaaS) 解決方案，其使用以模型為基礎的方法，讓您建置企業級 IoT 解決方案，而不需要雲端解決方案的開發專業。

## <a name="azure-iot-hub"></a>Azure IoT 中樞

Azure IoT 中樞是 Azure IoT 中心和 Azure IoT 解決方案加速器都會使用的核心 Azure PaaS。 IoT 中樞可在數百萬個 IoT 裝置和一個雲端解決方案之間啟用可靠且安全的雙向通訊。 IoT 中樞可協助您因應 IoT 實作挑戰，例如：

* 大量裝置的連線能力和管理。
* 大量的遙測資料擷取。
* 裝置的命令與控制。
* 裝置安全性的強制執行。

## <a name="compare-azure-iot-solution-accelerators-and-azure-iot-central"></a>比較 Azure IoT 解決方案加速器與 Azure IoT 中心

選擇 Azure IoT 產品是規劃您 IoT 解決方案的重要部分。 IoT 中樞是獨立的 Azure 服務，本身不會提供端對端的 IoT 解決方案。 IoT 中樞可以作為任何 IoT 解決方案的起點，而且不需要透過 Azure IoT 解決方案加速器或 Azure IoT 中心就可使用。 Azure IoT 解決方案加速器和 Azure IoT 中心都會使用 IoT 中樞與其他 Azure 服務。 下表彙總了 Azure IoT 解決方案加速器和 Azure IoT 中心之間的主要差異，可協助您根據需求做出正確的選擇：

|                        | Azure IoT 解決方案加速器 | Azure IoT 中心 |
| ---------------------- | --------- | ----------- |
| 主要用途 | 加速開發需要最大彈性的自訂 IoT 解決方案。 | 加速簡易 IoT 解決方案上市時間，不需要深入的服務自訂。 |
| 存取基礎 PaaS 服務          | 您可存取基礎 Azure 服務來加以管理，或視需要加以取代。 | SaaS。 完全受控的解決方案，不會公開基礎服務。 |
| 彈性            | 高。 微服務的程式碼是開放原始碼，您可以使用任何您認為合適的方式修改它。 此外，您可以自訂部署基礎結構。| 中。 您可以使用以內建瀏覽器為基礎的使用者體驗，自訂解決方案模型和 UI 層面。 基礎結構無法自訂，因為並不會公開各種元件。|
| 技能層級                 | 中高。 需要 Java 或 .NET 技能來自訂解決方案後端。 您需要 JavaScript 技能來自訂視覺效果。 | 低。 您需要有建立模型的技能來自訂解決方案。 不需要編寫程式碼的技能。 |
| 上手體驗 | 解決方案加速器可實作常見的 IoT 案例。 可在幾分鐘內部署完成。 | 應用程式範本和裝置範本會提供預先建置的模型。 可在幾分鐘內部署完成。 |
| 價格                | 您可以微調該服務以控制成本。 | 簡單且可預測的定價結構。 |

決定使用哪個產品來建置 IoT 解決方案的最重要因素如下：

* 您的業務需求。
* 您要建置的解決方案類型
* 以長期來看，貴組織建置與維護解決方案的一套技能。

## <a name="next-steps"></a>後續步驟

根據您所選擇的產品和方法，建議的後續步驟為：

* **Azure IoT 解決方案加速器**：[什麼是 Azure IoT 解決方案加速器？](iot-accelerators-what-are-solution-accelerators.md)
* **Azure IoT 中心**：[Azure IoT 中心](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions)。
* **IoT 中樞**：[Azure IoT 中樞服務的概觀](../iot-hub/iot-hub-what-is-iot-hub.md)。
