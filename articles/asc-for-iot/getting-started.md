---
title: 開始使用 IoT 預覽的 Azure 資訊安全中心 (ASC) |Microsoft Docs
description: 開始了解 Azure 資訊安全中心的 IoT 功能和服務的基本工作流程。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: bdc5e858286d7db03281408cf3ed00e0760e0c3a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65200635"
---
# <a name="get-started-with-azure-security-center-for-iot"></a>開始使用 Azure 資訊安全中心的 IoT 

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章提供不同的建置組塊的 Azure 資訊安全中心 (ASC) 說明 IoT 服務，並說明如何開始使用[啟用服務](quickstart-onboard-iot-hub.md)。 

Iot 的 ASC 可以順暢地整合到您的 IoT 中樞，以提供安全性分析 IoT 中樞組態、 裝置身分識別和中樞裝置通訊模式。
增強式的安全性功能，ASC iot 會提供代理程式為基礎的集合，從您的 IoT 裝置的安全性資料。

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>IoT 無縫式的 IoT 中樞整合 ASC

嘗試將保護個別的 IoT 裝置時，可以直接從裝置，或其網路收集資料需要。 若要支援這項工作，ASC iot 會提供許多低耗用量安全性代理程式，以提供裝置監視和強化。

在 ASC IoT preview 中，參考架構的 Linux 和 Windows 安全性代理程式，在C#和 C 會提供。
代理程式會處理未經處理的事件集合，從裝置的作業系統，以降低成本，並透過裝置模組對應項進行設定的事件彙總。
安全性訊息透過 IoT 中樞，傳送到 ASC IoT 分析服務。

## <a name="asc-for-iot-basics"></a>ASC 代表 IoT 的基本概念

選擇最符合您的 IoT 裝置和環境需求的工作流程案例：

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>開始使用 ASC IoT 無縫式的 IoT 中樞整合 

>[!Note]
>此工作流程可讓您使用不包含 ASC IoT 安全性代理程式的服務。 

若要啟用監視您的裝置身分識別管理，裝置到雲端和雲端到裝置通訊模式，使用下列基本工作流程進行測試，然後啟動服務： 

1. [在您的 IoT 中樞的 IoT 服務啟用 ASC](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中樞都不有任何已註冊的裝置[註冊新裝置](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. [建立您的裝置的 azureiotsecurity 安全性模組](quickstart-create-security-twin.md)為您的裝置。 
1. 定義透過一般的裝置和系統行為[自訂警示](quickstart-create-custom-alerts.md)。 
1. 執行系統測試，以確認服務和裝置的狀態。 
1. 瀏覽[警示](concept-security-alerts.md)，[建議](concept-recommendations.md)，並[使用 Log Analytics 的深入探討](how-to-security-data-access.md)使用 IoT 中樞。 


### <a name="get-started-with-asc-for-iot-security-agents"></a>開始使用 ASC IoT 安全性代理程式

利用 ASC 的 IoT 增強式安全性功能，例如測試，並啟用服務使用下列的基本工作流程監視遠端連線、 使用中的應用程式、 登入事件和作業系統組態的最佳作法： 

1. [啟用 ASC 以您的 IoT 中樞的 IoT 服務](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中樞都不有任何已註冊的裝置[註冊新裝置](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. [建立 azureiotsecurity 安全性模組](quickstart-create-security-twin.md)為您的裝置。
1. 在 Azure 的模擬裝置，而不是安裝在實際的裝置上安裝代理程式[微調註冊新的 Azure 虛擬機器 (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)中可用的區域。 
1. [部署 IoT 安全性代理程式 ASC](how-to-deploy-linux-cs.md)上您的 IoT 裝置或新的 VM。
1. 請依照下列指示[trigger_events](https://aka.ms/iot-security-github-trigger-events)執行無害攻擊的模擬。
1. IoT 中的警示回應模擬攻擊，在上一個步驟中，確認 ASC。 開始驗證五分鐘後執行指令碼。
1. 瀏覽[警示](concept-security-alerts.md)，[建議](concept-recommendations.md)，並[使用 Log Analytics 的深入探討](how-to-security-data-access.md)使用 IoT 中樞。 

## <a name="next-steps"></a>後續步驟

- 啟用[ASC iot](quickstart-onboard-iot-hub.md)
- 設定您[解決方案](quickstart-configure-your-solution.md)
- [建立安全性模組](quickstart-create-security-twin.md)
- 設定[自訂警示](quickstart-create-custom-alerts.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
