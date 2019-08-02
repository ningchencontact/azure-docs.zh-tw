---
title: 將 Syslog 資料連線到 Azure Sentinel 預覽 |Microsoft Docs
description: 瞭解如何將 Syslog 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: dad74410562aa54aeb61675e7dc1c0adccc44797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679218"
---
# <a name="connect-your-external-solution-using-syslog"></a>使用 Syslog 連接您的外部解決方案

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以將任何支援 Syslog 的內部部署設備連線到 Azure Sentinel。 這項作業是使用以設備和 Azure Sentinel 之間的 Linux 機器為基礎的代理程式來完成。 如果您的 Linux 機器位於 Azure 中, 您可以將應用程式中的記錄串流至您在 Azure 中建立的專用工作區, 並加以連接。 如果您的 Linux 電腦不在 Azure 中, 您可以將應用程式中的記錄串流至專用的內部部署 VM 或電腦上, 以安裝 Linux 的代理程式。 

> [!NOTE]
> 如果您的設備支援 Syslog CEF, 連線就會更完整, 而且您應該選擇此選項, 並遵循[從 CEF 連接資料](connect-common-event-format.md)中的指示。

## <a name="how-it-works"></a>運作方式

Syslog 是通用於 Linux 的事件記錄通訊協定。 應用程式將傳送的訊息可能會儲存在本機電腦上，或傳遞到 Syslog 收集器。 安裝 Log Analytics Linux 代理程式時，它會設定本機 Syslog 精靈來將訊息轉送到代理程式。 然後，代理程式會將訊息傳送至 Azure 監視器 (建立相對應記錄的位置)。

如需詳細資訊, 請參閱[Azure 監視器中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)。

> [!NOTE]
> 代理程式可以從多個來源收集記錄, 但必須安裝在專用的 proxy 電腦上。

## <a name="connect-your-syslog-appliance"></a>連接您的 Syslog 設備

1. 在 Azure Sentinel 入口網站中, 選取 [**資料連線器**], 然後選取資料表中的**syslog**行, 並在右側的 syslog 窗格中, 按一下 [**開啟連接器] 頁面**。
2. 如果您的 Linux 機器位於 Azure 中, 請選取 [**在 Azure Linux 虛擬機器上下載並安裝代理程式**]。 在 [虛擬機器] 視窗中, 選取您要安裝代理程式的電腦, 然後按一下頂端的 **[連線]** 。
1. 如果您的 Linux 機器不在 Azure 內, 請選取 [**在 linux 非 azure 機器上下載並安裝代理程式**]。 在 [**直接代理程式**] 視窗中, 複製 [**下載並上架代理程式**] 底下的命令, 並在您的電腦上執行。 
   > [!NOTE]
   > 請務必根據貴組織的安全性原則來設定電腦的安全性。 例如, 您可以設定您的網路以配合公司網路安全性原則, 並變更背景程式中的埠和通訊協定, 以符合您的需求。 

1. 在 [設定要在 Syslog 連接器安裝程式中**連線的記錄**] 底下, 遵循下列指示:
    1. 按一下連結以**開啟您的工作區 [advanced settings] 設定**。 
    1. 依序選取 [**資料**] 和 [ **Syslog**]。
    1. 然後, 在資料表中設定您想要 Syslog 收集的設備。 您應該在其記錄標頭中新增或選取 Syslog 應用裝置所包含的功能。 您可以在/etc/rsyslog.d/security-config-omsagent.conf 的 Syslog 設備中看到此設定, 並在/etc/syslog-ng/security-config-omsagent.conf. 下的 r-Syslog 中查看 
       > [!NOTE]
       > 如果您選取 [**將下列設定套用到我的電腦**] 核取方塊, 則此設定會套用到所有連線到此工作區的 Linux 電腦。 您可以在您的 Syslog 電腦中看到這項設定 
1. 按一下 **[按這裡] 以開啟 [** 設定] 分頁。
1. 依序選取 [**資料**] 和 [ **Syslog**]。
   - 請確定您要由 Syslog 傳送的每個設備都在資料表中。 針對每個設施, 您要監視, 設定嚴重性。 按一下 **[套用]** 。
1. 在您的 Syslog 電腦中, 請確定您要傳送這些設備。 

1. 若要在 Log Analytics 中針對 Syslog 記錄使用相關的架構, 請搜尋**syslog**。
1. 您可以使用在[Azure 監視器記錄查詢中使用](../azure-monitor/log-query/functions.md)函式中所述的 Kusto 函式來剖析 Syslog 訊息, 然後將其儲存為新的 log Analytics 函式, 然後使用該函數做為新的資料類型。




## <a name="next-steps"></a>後續步驟
在本檔中, 您已瞭解如何將 Syslog 內部部署應用裝置連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
