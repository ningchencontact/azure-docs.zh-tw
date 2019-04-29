---
title: 了解 Azure 資訊安全中心的 IoT 解決方案架構預覽 |Microsoft Docs
description: 深入了解 Azure 資訊安全中心，IoT 服務的資訊流程。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: a0eb459391da65f8d0e2ae251809805924d07ad1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61360378"
---
# <a name="azure-security-center-for-iot-architecture"></a>IoT 架構的 azure 資訊安全中心

這篇文章說明功能的系統架構的 Azure 資訊安全中心 (ASC) 的 IoT 解決方案。 

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="asc-for-iot-components"></a>ASC IoT 元件

Iot 的 ASC 是由下列元件組成：
- 裝置代理程式
- 傳送安全訊息 SDK
- IoT 中樞的整合
- 分析管線
 
### <a name="asc-for-iot-workflow"></a>ASC IoT 工作流程

ASC IoT 裝置的代理程式可讓您輕鬆地從您的裝置收集未經處理的安全性事件。 未經處理的安全性事件可能包括 IP 連線、 建立處理程序、 使用者登入和其他安全性相關資訊。 ASC IoT 裝置的代理程式也會處理事件的彙總，來協助避免高網路輸送量。 代理程式是高度可自訂，避免成本較高的服務，讓您針對特定的工作，例如傳送重要資訊只有最快的 sla，或彙總成更大的區段的廣泛的安全性資訊和內容，請使用它們。
 
裝置代理程式和其他應用程式使用**Azure ASC 傳送安全訊息 SDK**傳送到 Azure IoT 中樞的安全性資訊。 IoT 中樞挑選此資訊，並將它轉送到 IoT 服務 ASC。

一旦啟用 ASC IoT 服務，除了轉送的資料，IoT 中樞也會將其內部資料以供分析，依遞增的所有 iot。 此資料包括裝置雲端作業記錄檔、 裝置身分識別和中樞組態。 所有的這項資訊可以協助建立 IoT 分析管線 ASC。
 
ASC IoT 分析管線也會收到額外的威脅情報資料流來自各種來源 Microsoft 與 Microsoft 合作夥伴。 IoT 分析整個管線 ASC 適用於每個對服務 （例如自訂警示和使用 「 傳送安全訊息 SDK） 的客戶組態。
 
使用分析管線，ASC iot 結合了所有的資訊來產生可採取動作的建議和警示資料流。 此管線包含安全性研究人員和專家，以及機器學習服務搜尋偏離標準的裝置行為和風險分析的模型所建立的兩個自訂規則。
 
ASC 代表 IoT 建議和警示 （分析管線輸出） 會寫入至 Log Analytics 工作區，每一位客戶。 在工作區，以及警示和建議中包含未經處理的事件，可讓深入探討調查，並使用偵測到的可疑活動的確切詳細資料的查詢。  

## <a name="next-steps"></a>後續步驟

在本文中，您已了解基本架構和 ASC 的 IoT 解決方案的工作流程。 若要深入了解必要條件，如何開始和啟用您的安全性解決方案，在 IoT 中樞，請參閱下列文章：

- [服務的必要條件](service-prerequisites.md)
- [開始使用](getting-started.md)
- [設定您的解決方案](quickstart-configure-your-solution.md)
- [啟用 IoT 中樞安全性](quickstart-onboard-iot-hub.md)
- [ASC iot 常見問題集](resources-frequently-asked-questions.md)
- [IoT 安全性警示的 ASC](concept-security-alerts.md)

