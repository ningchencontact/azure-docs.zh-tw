---
title: 了解 IoT Edge 的 ASC IoT 安全性模組 |Microsoft Docs
description: 了解 IoT Edge 的架構和 ASC 的 IoT 安全性模組的功能。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 68117370e21c7b667bde5447cef510a4e7c77df0
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580759"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge 安全性模組

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版的服務等級協定，不提供，且不建議用於生產環境 worklo§1ads。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/)提供功能強大的功能，可管理，以及執行在邊緣的商務工作流程。
IoT Edge 在 IoT 環境中所扮演的重要部分讓它特別能吸引針對惡意執行者。

IoT 安全性模組的 ASC 提供全方位的安全性解決方案適用於您的 IoT Edge 裝置。
ASC IoT 模組會收集、 彙總並分析未經處理的安全性資料，從您的作業系統和容器系統，到可操作的安全性建議和警示。

類似於 ASC IoT 裝置的 IoT 安全性代理程式，IoT Edge 模組 ASC 是高度可自訂，透過其模組對應項。
請參閱[設定代理程式](how-to-agent-configuration.md)若要深入了。

IoT Edge 適用的 IoT 安全性模組的 ASC 提供下列功能：

- 從基礎作業系統 (Linux) 和 IoT Edge 容器系統，會收集未經處理的安全性事件。
  
  請參閱[IoT 代理程式設定的 ASC](how-to-agent-configuration.md)若要深入了解可用的安全性資料收集器。

- IoT Edge 部署資訊清單的分析。

- 透過傳送訊息到彙總未經處理的安全性事件[IoT Edge 中樞](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-runtime#iot-edge-hub)。

- 移除透過設定使用的安全性模組對應項。

  請參閱[設定 IoT 代理程式 ASC](how-to-agent-configuration.md)若要深入了。

ASC IoT Edge 適用的 IoT 安全性模組會在 IoT Edge 在特權模式下執行。
特殊權限的模式，才能讓監視作業系統中，模組和其他 IoT Edge 模組。

## <a name="agent-supported-platforms"></a>支援的代理程式的平台

IoT Edge 適用的 IoT 安全性模組的 ASC 是目前僅適用於 Linux。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解的架構和 ASC 的 IoT 安全性模組的功能適用於 IoT Edge。

若要繼續開始使用 ASC 針對 IoT 部署，請使用下列文章：

- 部署[IoT Edge 的安全性模組](how-to-deploy-edge.md)
- 了解如何[設定安全性模組](how-to-agent-configuration.md)
- 檢閱 ASC iot[服務必要條件](service-prerequisites.md)
- 了解如何[啟用 ASC 中您的 IoT 中樞的 IoT 服務](quickstart-onboard-iot-hub.md)
- 深入了解從服務[ASC IoT 常見問題集](resources-frequently-asked-questions.md)