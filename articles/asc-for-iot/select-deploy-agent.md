---
title: 選取和部署 IoT 代理程式預覽 ASC |Microsoft Docs
description: 了解關於如何選取和部署 IoT 裝置上的 IoT 安全性代理程式 ASC。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5ce583fe98acb7e0a4aaeb720cb2d5ed5eebb9e3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541958"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>選取並部署您的 IoT 裝置上的安全性代理程式

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


ASC iot 會提供參考架構的安全性監視和收集資料，從 IoT 裝置的代理程式。

安全性代理程式會開發做為開放原始碼專案，並有兩種類別： <br> [C](https://aka.ms/iot-security-github-c)，並[ C# ](https://aka.ms/iot-security-github-cs)。

## <a name="supported-platforms-for-asc-for-iot-agents"></a>支援的平台，ASC IoT 代理程式

下列清單包含所有目前支援的平台。  

|ASC IoT 代理程式 |作業系統 |架構 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64、 x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core 組建 17763 |x64|


## <a name="which-flavor-should-i-use"></a>我應該使用哪一個類別？

請考慮下列問題，對於您的 IoT 裝置：

- 您會使用_Windows Server_或是_Windows IoT 核心版_嗎？ 

    使用[ASC 與 Windows 的 IoT 安裝C#-基礎安全性代理程式](tutorial-deploy-windows-cs.md)。

- 您使用 Linux 散發套件搭配 x86 架構？ 

    使用[ASC 與 C 為基礎的安全性代理程式適用於 Linux 的 IoT 安裝](tutorial-deploy-linux-c.md)。
- 您使用 Linux 散發套件為 x64 架構？

    您可以使用這兩個類別。 <br>
    [與 C 為基礎的安全性代理程式的 IoT 安裝適用於 Linux 的 ASC](tutorial-deploy-linux-c.md)及/或[ASC IoT 安裝適用於 Linux 與C#-基礎安全性代理程式](tutorial-deploy-linux-cs.md)。

這兩個類別有一組相同的功能，並支援類似的組態選項。 C 為基礎的安全性代理程式具有較低的記憶體耗用量。


## <a name="next-steps"></a>後續步驟
- [概觀](overview.md)
- [安全性代理程式](security-agent-architecture.md)
- [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)
- [ASC iot 常見問題集](resources-frequently-asked-questions.md)