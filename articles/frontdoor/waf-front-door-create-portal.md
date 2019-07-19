---
title: 使用 Azure 入口網站建立 Azure Front 的 web 應用程式防火牆原則
titlesuffix: Azure web application firewall
description: 瞭解如何使用 Azure 入口網站建立 web 應用程式防火牆 (WAF) 原則。
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: abaef0fb521d848134885a06591b0656c60c67e6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846380"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure Front 的 WAF 原則

本文說明如何建立基本的 Azure web 應用程式防火牆 (WAF) 原則, 並將它套用至 Azure Front 的前端主機。

## <a name="prerequisites"></a>先決條件

請遵循[快速入門：建立 Front Door 設定檔](quickstart-create-front-door.md)中所述的指示來建立 Front Door 設定檔。 

## <a name="create-a-waf-policy"></a>建立 WAF 原則

首先, 使用入口網站來建立具有受控預設規則集 (DRS) 的基本 WAF 原則。 

1. 在畫面的左上方, 選取 [**建立資源**] > 搜尋**WAF**> 選取 [ **Web 應用程式防火牆 (預覽)** ] > 選取 [**建立**]。
2. 在 [**建立 WAF 原則**] 頁面的 [**基本**] 索引標籤中, 輸入或選取下列資訊、接受其餘設定的預設值, 然後選取 [**檢查 + 建立**]:

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | Subscription            |選取您的 Front 入口訂用帳戶名稱。|
    | Resource group          |選取您的 Front 資源組名。|
    | 原則名稱             |輸入 WAF 原則的唯一名稱。|

   ![建立 WAF 原則](./media/waf-front-door-create-portal/basic.png)

3. 在 [**建立 WAF 原則**] 頁面的 [**關聯**] 索引標籤中, 選取 [**新增前端主機**], 輸入下列設定, 然後選取 [**新增**]:

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | Front              | 選取您的前門設定檔名稱。|
    | 前端主機           | 選取您的前端主機名稱, 然後選取 [**新增**]。|
    
    > [!NOTE]
    > 如果前端主機與 WAF 原則相關聯, 它會顯示為灰色。您必須先從相關聯的原則中移除前端主機, 然後再將前端主機重新關聯至新的 WAF 原則。
1. 選取 [**審查 + 建立**], 然後選取 [**建立**]。

## <a name="configure-waf-rules-optional"></a>設定 WAF 規則 (選擇性)

### <a name="change-mode"></a>變更模式

當您建立 WAF 原則時, 預設的 WAF 原則會處於**偵測**模式。 在**偵測**模式中, WAF 不會封鎖任何要求, 而是會在 WAF 記錄中記錄符合 WAF 規則的要求。
若要查看作用中的 WAF, 您可以將模式設定從**偵測**變更為**預防**。 在**預防**模式中, 會封鎖與預設規則集 (DRS) 中定義之規則相符的要求, 並記錄在 WAF 記錄中。

 ![變更 WAF 原則模式](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>自訂規則

您可以選取 [**自訂規則**] 區段底下的 [**新增自訂規則**] 來建立自訂規則。 這會啟動 [自訂規則設定] 頁面。 以下範例說明如何設定自訂規則, 以在查詢字串包含**blockme**時封鎖要求。

![變更 WAF 原則模式](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>預設規則集 (DRS)

預設會啟用 Azure 管理的預設規則集。 若要停用規則群組中的個別規則, 請展開該規則群組中的規則, 選取規則編號前面的**核取方塊**, 然後選取上方索引標籤上的 [**停**用]。 若要變更規則集內個別規則的動作類型, 請選取規則編號前面的核取方塊, 然後選取上方的 [**變更動作**] 索引標籤。

 ![變更 WAF 規則集](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Web 應用程式防火牆](waf-overview.md)。
- 深入瞭解[Azure Front 大門](front-door-overview.md)。
