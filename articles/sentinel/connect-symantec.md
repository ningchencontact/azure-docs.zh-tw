---
title: Symantec ICDx 資料連接至 Azure 的 Sentinel 預覽 |Microsoft Docs
description: 了解如何將 Symantec ICDx 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 74169b4bd2654fb0ff7ec4cdb2f2b02c0f4cc6e8
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673744"
---
# <a name="connect-your-symantec-icdx-appliance"></a>連接您的 Symantec ICDx 應用裝置 

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Symantec ICDx 連接器可讓您輕鬆地連接所有您 Symantec 安全性解決方案記錄您 Azure Sentinel，來檢視儀表板、 建立自訂警示，以及改善調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。 Symantec ICDx 與 Azure Sentinel 之間的整合會使用 REST API。


> [!NOTE]
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="configure-and-connect-symantec-icdx"></a>設定及連接 Symantec ICDx 

Symantec ICDx 整合和記錄檔將直接匯出至 Azure 的 Sentinel。

1. 開啟 ICDx 管理主控台，將 Microsoft Azure Sentinel (Log Analytics) 轉寄站。
2. ICDx 導覽列中，按一下 **組態**。 
3. 在頂端**組態**畫面上，按一下**轉寄站**。
4. 底下**轉寄站**，旁邊 Microsoft Azure Sentinel (Log Analytics)，按一下 **新增**。 
4. 在 [ **Microsoft Azure Sentinel (Log Analytics)** ] 視窗中，按一下**顯示進階**。 
5. 頂端的 [展開至 Microsoft Azure Sentinel (Log Analytics)] 視窗中，執行下列動作：
    -   **名稱**：輸入轉寄站已超過 30 個字元的名稱。 選擇唯一的、 有意義的名稱。 此名稱的轉寄站清單中會出現在**組態**螢幕和中的儀表板上**儀表板**螢幕。 例如: Microsoft Azure Log Analytics 東部。 這是必填欄位。
    -   **描述**：請輸入轉寄站的描述。 此描述也會出現在轉寄站清單上**組態**螢幕。 包含詳細資料，例如正在轉送的事件類型和群組需要檢查的資料。
    -   **啟動類型**:選取啟動的轉寄站組態的方法。 您的選項是手動和自動。<br>預設為自動。 
6. 底下**事件**，執行下列動作： 
    - **來源**：選取要從中轉寄事件的一或多個封存。 您可以選取使用中的收集器封存 （包括常見的封存），被遺棄行程封存 （也就是您已刪除的收集器封存）、 ICDx 接收者封存或系統保存檔。 <br>預設值是常見的封存。
      > [!NOTE]
      > ICDx 接收者封存會分別依名稱列出。 
 
    - **篩選條件**：加入篩選，指定要轉送的事件子集。 執行下列其中一項：
        - 若要選取的篩選條件，按一下 型別、 屬性、 運算子和值。 
        - 在 [篩選] 欄位中，檢閱您的篩選條件。 您可以直接在欄位中編輯它，或視需要將它刪除。
        - 按一下，或加入篩選條件。
        - 您也可以按一下 儲存的查詢，以套用已儲存的查詢。
    - **包含屬性**:輸入以逗號分隔清單，要在轉送的資料中包含的屬性。 包含的屬性會優先於排除的屬性。
    - **排除的屬性**:輸入以逗號分隔清單，要排除的轉送資料的屬性。
    - **批次大小**:選取要傳送每個批次的事件數目。 您的選項是 10，50、 100、 500 到 1000年。<br>預設值為 100。 
    - **速率限制**:選取的轉送事件，以每秒事件的速率。 您的選項為無限制，500、 1000、 5000、 10000。 <br> 預設值為 5000。 
7. 底下**Azure 目的地**，執行下列動作： 
    - **工作區識別碼**:從下面貼上的工作區識別碼。 這是必填欄位。
    - **主索引鍵**:從下面貼上的主索引鍵。 這是必填欄位。
    - **自訂記錄檔名稱**:輸入 Microsoft Azure 入口網站 Log Analytics 工作區中您會將事件轉送的自訂記錄檔名稱。 預設為 SymantecICDx。 這是必填欄位。
8. 按一下 *儲存*來完成轉寄站設定。 
9. 若要啟動轉寄站，底下**選項**，按一下**詳細**，然後**啟動**。
10. 若要使用 Log Analytics 中的 Symantec ICDx 事件相關的結構描述，搜尋**SymantecICDx_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Symantec ICDx。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

