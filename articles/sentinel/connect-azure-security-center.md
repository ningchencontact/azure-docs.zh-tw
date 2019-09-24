---
title: 將 Azure 資訊安全中心資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Azure 資訊安全中心資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240227"
---
# <a name="connect-data-from-azure-security-center"></a>從 Azure 資訊安全中心連接資料





Azure Sentinel 可讓您從[Azure 資訊安全中心](../security-center/security-center-intro.md)連接警示，並將它們串流至 Azure Sentinel。 

## <a name="prerequisites"></a>必要條件

- 如果您想要從 Azure 資訊安全中心匯出警示，您必須是您要串流其記錄之訂用帳戶上的參與者。

- 您必須在訂用帳戶上執行[Azure 資訊安全中心標準層](../security-center/security-center-pricing.md)。 如果不是，請將[您的訂用帳戶升級為標準版](https://azure.microsoft.com/pricing/details/security-center/)。

- 您必須以具有全域管理員或安全性系統管理員許可權的使用者身分，登入您想要連線的每個訂用帳戶。


## <a name="connect-to-azure-security-center"></a>連接到 Azure 資訊安全中心

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure 資訊安全中心**] 磚。

1. 在右側，按一下每個您想要串流其警示的訂用帳戶旁邊的 **[連線]** Azure Sentinel。 請務必將每個訂用帳戶升級為 Azure 資訊安全中心標準層，以將警示串流至 Azure Sentinel。

1. 您可以選取是否要讓警示 Azure 資訊安全中心自動在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**啟用**] 以啟用預設分析規則，以自動從已連線的安全性服務中產生的警示建立事件。 接著，您可以在 [**分析**] 和 [作用中**規則**] 底下編輯此規則。

3. 按一下 **[連接]** 。

4. 若要在 Log Analytics 中針對 Azure 資訊安全中心警示使用相關的架構，請搜尋**SecurityAlert**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 資訊安全中心連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
