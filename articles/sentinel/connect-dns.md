---
title: 連接 Azure Sentinel 預覽版中的 DNS 資料 |Microsoft Docs
description: 了解如何在 Azure Sentinel 的 DNS 資料連接。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6429568b33ece3ed4f26614e55e8c3069dd65d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204414"
---
# <a name="connect-your-domain-name-server"></a>連接您的網域名稱伺服器

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以連接任何網域名稱伺服器 (DNS) 上執行 Windows Azure 的 Sentinel。 這是 DNS 的電腦上安裝代理程式。 使用 DNS 記錄，您可以取得安全性、 效能及作業相關深入了解您的組織的 DNS 基礎結構收集、 分析和相互關聯分析和稽核記錄檔及其他相關資料，從 DNS 伺服器。

當您啟用 DNS 記錄的連線時，您可以：
- 指出嘗試解析惡意網域名稱的用戶端
- 指出過時的資源記錄
- 指出經常查詢的網域名稱和 Talkative DNS 用戶端
- 檢視 DNS 伺服器上的要求負載
- 檢視動態 DNS 註冊失敗

## <a name="how-it-works"></a>運作方式

DNS 連線被透過 DNS 電腦上安裝代理程式。 代理程式會從 DNS 中提取事件，並將它們傳送至 Log Analytics。

## <a name="connect-your-dns-appliance"></a>連接您的 DNS 設備

1. 在 Azure Sentinel 入口網站中，選取**資料連接器**，然後選擇**DNS**圖格。
1. 如果您的 DNS 機器是在 Azure 中：
    1. 按一下 **下載並安裝代理程式的 Windows 虛擬機器**。
    1. 在 **虛擬機器**清單中，選取您想要串流處理至 Azure 的 Sentinel DNS 機器。 請確定這是 Windows VM。
    1. 在視窗中開啟該 vm，按一下**Connect**。  
    1. 按一下 **啟用**中**DNS 連接器**視窗。 

2. 如果您的 DNS 電腦不是 Azure VM:
    1. 按一下 **下載並安裝代理程式，適用於 Windows 非 Azure 機器**。
    1. 在 [**直接代理程式**] 視窗中，選取**下載 Windows 代理程式 （64 位元）** 或是**下載 Windows 代理程式 （32 位元）** 。
    1. 在您 DNS 的電腦上安裝代理程式。 複製**工作區識別碼**，**主索引鍵**，並**次要金鑰**和使用它們在安裝期間出現提示時。

3. 若要使用 Log Analytics 中的 DNS 記錄相關的結構描述，搜尋**DnsEvents**。

## <a name="validate"></a>驗證 

在 Log Analytics 搜尋結構描述**DnsEvents** ，並確定有事件。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 DNS 內部部署設備。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
