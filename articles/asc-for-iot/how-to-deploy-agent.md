---
title: 選取和部署 IoT 代理程式預覽的 Azure 資訊安全中心 |Microsoft Docs
description: 了解關於如何選取和部署 Azure 資訊安全中心的 IoT 裝置上的 IoT 安全性代理程式。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358398"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>選取並部署您的 IoT 裝置上的安全性代理程式

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IoT 的 azure 資訊安全中心 (ASC) 提供參考架構的安全性監視和收集資料，從 IoT 裝置的代理程式。
請參閱[安全性代理程式參考架構](security-agent-architecture.md)若要深入了。

代理程式會開發做為開放原始碼專案，並且提供兩種類別： <br> [C](https://aka.ms/iot-security-github-c)，並[ C# ](https://aka.ms/iot-security-github-cs)。

在本文中，您將了解： 
> [!div class="checklist"]
> * 比較安全性代理程式類別
> * 探索支援的代理程式的平台
> * 選擇正確的代理程式類別，為您的方案

## <a name="understand-security-agent-options"></a>了解安全性代理程式選項

IoT 安全性代理程式標註的每個 ASC 提供一組相同的功能，並支援類似的組態選項。 

C 為基礎的安全性代理程式具有較低的記憶體耗用量，而且使用較少的可用資源進行裝置的理想選擇。 

|     | C 為基礎的安全性代理程式 | C#-基礎安全性代理程式 |
| --- | ----------- | --------- |
| 開放原始碼 | 底下[MIT license](https://en.wikipedia.org/wiki/MIT_License)在[Github](https://aka.ms/iot-security-github-cs) | 底下[MIT license](https://en.wikipedia.org/wiki/MIT_License)在[Github](https://aka.ms/iot-security-github-c) |
| 開發語言    | C | C# |
| 支援的 Windows 平台？ | 否 | 是 |
| Windows 必要條件 | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| 支援的 Linux 平台？ | 是，x64 和 x86 | 是，僅限 x64 |
| Linux 必要條件 | libunwind8、 libcurl3、 uuid 執行階段、 auditd、 audispd 外掛程式 | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| 磁碟使用量 | 10.5 MB | 90MB |
| （在平均） 的記憶體耗用量 | 5.5 MB | 33MB |
| [驗證](concept-security-agent-authentication-methods.md)到 IoT 中樞 | 是 | 是 |
| 安全性資料[集合](how-to-agent-configuration.md#supported-security-events) | 是 | 是 |
| 事件彙總 | 是 | 是 |
| 透過遠端組態[安全性模組對應項](concept-security-module.md) | 是 | 是 |


## <a name="choose-an-agent-flavor"></a>選擇代理程式類別 

回答下列有關您的 IoT 裝置來選取正確的代理程式問題：

- 您會使用_Windows Server_或是_Windows IoT 核心版_嗎？ 

    [部署C#為基礎的 Windows 安全性代理程式](how-to-deploy-windows-cs.md)。

- 您使用 Linux 散發套件搭配 x86 架構？ 

    [部署適用於 Linux 的 C 為基礎的安全性代理程式](how-to-deploy-linux-c.md)。

- 您使用 Linux 散發套件為 x64 架構？

    您可以使用任一代理程式類別。 <br>
    [部署適用於 Linux 的 C 為基礎的安全性代理程式](how-to-deploy-linux-c.md)及/或[部署C#-基礎安全性代理程式，適用於 Linux](how-to-deploy-linux-cs.md)。

這兩個代理程式類別提供一組相同的功能，並支援類似的組態選項。
請參閱[安全性代理程式比較](how-to-deploy-agent.md#understand-security-agent-options)若要深入了。

## <a name="supported-platforms"></a>支援的平台

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

## <a name="next-steps"></a>後續步驟

若要深入了解組態選項，繼續代理程式設定的使用說明指南。 
> [!div class="nextstepaction"]
> [代理程式設定使用說明指南](./how-to-agent-configuration.md)
