---
title: 了解 IoT 安全性代理程式架構預覽 ASC |Microsoft Docs
description: 在 ASC 用於 IoT 服務的代理程式，了解安全性代理程式架構。
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
ms.openlocfilehash: 7c8f256cf91a479c45f21b933efdb6a5e0212796
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541868"
---
# <a name="security-agent-reference-architecture"></a>安全性代理程式參考架構

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版的服務等級協定，不提供，且不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


ASC iot 會提供參考架構的安全性記錄、 處理、 彙總，以及傳送到 IoT 中樞的安全性資料的代理程式。

安全性代理程式設計來在受條件約束的 IoT 環境中，運作，並可高度自訂方面提供相較於它們所取用的資源的值。

安全性代理程式支援下列的 IoT 解決方案功能：

- 從基礎 OS （Linux、 Windows），收集未經處理的安全性事件。 若要深入了解可用的安全性資料收集器，請參閱[IoT 代理程式設定的 ASC](concept-agent-configuration.md)。

- 將透過 IoT 中樞傳送的訊息的彙總的原始安全性事件。

- 使用現有的裝置身分識別或專用的模組身分識別進行驗證。 請參閱[安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)若要深入了。

- 設定從遠端透過善用**ascforiot**模組對應項。 若要進一步了解，請參閱[設定 IoT 代理程式 ASC](concept-agent-configuration.md)。

ASC 建立 IoT 安全性代理程式會開發做為開放原始碼專案，而且可從 GitHub: 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>支援的代理程式的平台

Iot 的 ASC 提供 32 位元和 64 位元 Windows，不同的安裝程式代理程式和適用於 32 位元和 64 位元 Linux。 請確定您有正確的代理程式安裝程式，您的裝置，根據下表的每個項目：

| 32 或 64 位元 |  Linux |  Windows |    詳細資料|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 位元  | C  | C#  ||
| 64 位元  | C#或 C           | C#      | 使用 C 代理程式具有最少資源的裝置|

## <a name="next-steps"></a>後續步驟

在本文中，您已了解 ASC IoT 安全性代理程式架構，和可用的安裝程式。

若要繼續開始使用 ASC 針對 IoT 部署，請使用下列文章：


- 檢閱 ASC iot[服務必要條件](service-prerequisites.md)
- 了解如何[啟用 ASC 中您的 IoT 中樞的 IoT 服務](quickstart-onboard-iot-hub.md)
- 使用快速入門以[設定您的解決方案](quickstart-configure-your-solution.md)
- 了解[安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- 選取和部署[安全性代理程式](select-deploy-agent.md)
- 深入了解從服務[ASC IoT 常見問題集](resources-frequently-asked-questions.md)