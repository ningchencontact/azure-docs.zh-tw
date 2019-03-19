---
title: 收集 Syslog 資料，在 Azure Sentinel 預覽 |Microsoft Docs
description: 了解如何收集 Syslog 資料，在 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55949da97f58f1d8c1670f69d25e92d6bb4e9eef
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447297"
---
# <a name="connect-your-external-solution-using-syslog"></a>連接您外部解決方案中使用 Syslog

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以連接支援 Azure Sentinel 的 Syslog 任何內部部署設備。 這是由使用代理程式之間的設備與 Azure Sentinel Linux 機器為基礎。 如果您的 Linux 機器是在 Azure 中，您可以串流處理來自您的應用裝置或應用程式以在 Azure 中建立，並將它連線的專用工作區的記錄檔。 如果您的 Linux 機器不是在 Azure 中，您可以串流處理記錄檔從您的應用裝置的專用內部部署 VM 或機器安裝 Linux 的代理程式上。 

> [!NOTE]
> 如果您的應用裝置支援 Syslog CEF 連線更完整，是您應該選擇此選項，並依照[CEF 會收集](connect-common-event-format.md)。

## <a name="how-it-works"></a>運作方式

Syslog 集合是使用適用於 Linux 的代理程式來完成。 根據預設，適用於 Linux 的代理程式會接收事件從 Syslog 精靈透過 UDP，但在 Linux 機器應該在其中收集大量的 Syslog 事件，例如 Linux 代理程式會收到事件時從其他裝置，若要修改設定的情況下使用 Syslog 精靈與代理程式之間的 TCP 傳輸。

## <a name="connect-your-syslog-appliance"></a>連接您的 Syslog 設備

1. 在 Azure Sentinel 入口網站中，選取**資料收集**，然後選擇**Syslog**圖格。
2. 如果您的 Linux 機器不是在 Azure 中，下載並安裝 Azure Sentinel**適用於 Linux 的代理程式**應用裝置上。 
1. 如果您正在 Azure 中，選取或建立 VM，Azure Sentinel 工作區，專門用來接收 Syslog 訊息中。 在 Azure Sentinel 工作區中選取 VM，然後按一下**Connect**在左窗格的頂端。
3. 按一下 **設定要收集記錄檔**年代 Syslog 連接器安裝程式。 
4. 按一下 **按這裡開啟 設定 刀鋒視窗**。
1. 選取 **資料**，然後**Syslog**。
   - 請確定您要傳送 Syslog 的每個設備是資料表中。 針對每個設備要監視、 設定嚴重性。 按一下 [套用]。
1. 在您 Syslog 的機器，請確定您要傳送這些設施。 

3. 若要使用 Log Analytics 中的 Syslog 記錄檔相關的結構描述，搜尋**Syslog**。




## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Syslog 內部部署設備。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
