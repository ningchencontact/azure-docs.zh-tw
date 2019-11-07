---
title: 使用 Azure CDN 標準規則引擎來強制執行 HTTPS |Microsoft Docs
description: 標準規則引擎可讓您自訂 Microsoft 的 Azure CDN 處理 HTTP 要求的方式，例如封鎖特定類型內容的傳遞、定義快取原則，以及修改 HTTP 標頭。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615919"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN 標準規則引擎

> [!NOTE]
> 標準規則引擎僅適用于來自 Microsoft 的 Azure CDN。 

Azure CDN 標準規則引擎可讓您自訂 HTTP 要求的處理方式。 例如，強制透過特定通訊協定傳遞內容、定義快取原則，或修改 HTTP 標頭。 本教學課程示範如何建立規則，以自動將您的使用者重新導向至 HTTPS。 


## <a name="tutorial"></a>教學課程

1. 從 [來自 Microsoft 的 Azure CDN 設定檔] 的 [ **cdn 設定檔**] 頁面，選取您想要設定規則的端點。
  
2. 選取左側的 [**規則引擎**] 索引標籤。
   
    [規則引擎] 分頁隨即顯示，其中包含全域規則。 
   
    [![CDN 新規則頁面](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 多項規則的列出順序會影響規則的處理方式。 後一項規則可能會覆寫前一項規則所指定的動作。
   >

3. 按一下 [**新增規則**] 按鈕，並提供 [規則名稱]。 規則名稱必須以字母開頭，且只包含數位和字母。

4. 識別要套用此規則的要求類型。 使用下拉式選來選取**要求通訊協定**比對條件，並使用**等於** **HTTP**的值。
   
   [![CDN 規則比對條件](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 下拉式清單中提供多個比對條件。 如需詳細的比對條件清單，請參閱[規則引擎比對條件](cdn-standard-rules-engine-match-conditions.md)。
   
5. 選擇將套用至已識別要求的動作。 使用下拉式清單來選取 [ **URL 重新導向**] 動作，並使用**找到的值（302）** 做為 [類型]，並使用**HTTPS**作為 [通訊協定]。 將所有其他欄位保留為空白，以使用傳入的值。
   
   [![CDN 規則動作](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 下拉式清單中有多個動作可供使用。 如需動作的詳細清單，請參閱[規則引擎動作](cdn-standard-rules-engine-actions.md)。

6. 選取 [**儲存**] 以儲存新的規則。  現在會部署新的規則。
   
   > [!IMPORTANT]
   > 規則變更最多可能需要15分鐘的時間，才能透過 Azure CDN 傳播。
   >
   

## <a name="see-also"></a>另請參閱

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎動作](cdn-standard-rules-engine-actions.md)
