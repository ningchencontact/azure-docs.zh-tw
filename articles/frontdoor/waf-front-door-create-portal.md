---
title: 使用 Azure 入口網站建立 web 應用程式防火牆原則的 Azure 大門
titlesuffix: Azure web application firewall
description: 了解如何使用 Azure 入口網站中建立 web 應用程式防火牆 (WAF) 原則。
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
ms.author: kumud;tyao
ms.openlocfilehash: 15a80dac0e0601480e22ad960f2827f3d8f290c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479043"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>使用 Azure 入口網站建立 WAF 原則的 Azure 大門

本文說明如何建立基本的 Azure web 應用程式防火牆 (WAF) 原則，並將它套用至前端的主機在 Azure 大門。

## <a name="prerequisites"></a>必要條件

請遵循[快速入門：建立 Front Door 設定檔](quickstart-create-front-door.md)中所述的指示來建立 Front Door 設定檔。 

## <a name="create-a-waf-policy"></a>建立 WAF 原則

首先，建立基本的 WAF 原則與受管理預設規則設定 (DRS) 使用入口網站。 

1. 在畫面左上方，選取**建立資源**> 搜尋**WAF**> 選取**Web 應用程式防火牆 （預覽）** > 選取**建立**。
2. 在 **基本概念**索引標籤**建立 WAF 原則**頁面上，輸入或選取下列資訊、 接受其餘的設定，預設值，然後選取**檢閱 + 建立**:

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 訂用帳戶            |選取您的大門訂用帳戶名稱。|
    | 資源群組          |選取您的大門資源群組名稱。|
    | 原則名稱             |輸入您的 WAF 原則的唯一名稱。|

   ![建立 WAF 原則](./media/waf-front-door-create-portal/basic.png)

3. 在 [**關聯**] 索引標籤**建立 WAF 原則**頁面上，選取**新增前端主機**，輸入下列設定，，然後選取**新增**:

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 大門              | 選取您的前端設定檔名稱。|
    | 前端主機           | 選取您前端主機名稱，然後選取**新增**。|
    
    > [!NOTE]
    > 如果前端主機與 WAF 原則相關聯的它會顯示為灰色。您必須先將前端主機移除相關聯的原則，並重新關聯到新的 WAF 原則前端主應用程式。
1. 選取 **檢閱 + 建立**，然後選取**建立**。

## <a name="configure-waf-rules-optional"></a>設定 WAF 規則 （選擇性）

### <a name="change-mode"></a>變更模式

當您建立 WAF 原則時，預設 waf 原則處於**偵測**模式。 在 **偵測**模式時，WAF 不會封鎖任何要求，相反地，比對的 WAF 規則的要求都會記錄在 WAF 記錄。
若要查看作用中的 WAF，您可以變更的模式設定**偵測**要**預防**。 在 **預防**模式中，要求所定義的預設規則設定 (DRS) 的比對規則會封鎖並記錄在 WAF 記錄。

 ![變更 WAF 原則模式](./media/waf-front-door-create-portal/policy.png)

### <a name="custom-rules"></a>自訂規則

您可以選取來建立自訂規則**新增的自訂規則**下方**自訂規則**一節。 這會啟動 [自訂規則組態] 頁面。 以下是設定自訂規則來封鎖要求，如果查詢字串中包含的範例**blockme**。

![變更 WAF 原則模式](./media/waf-front-door-create-portal/customquerystring2.png)

### <a name="default-rule-set-drs"></a>預設規則集 (DRS)

預設會啟用 azure 受管理預設規則集。 若要停用個別規則的規則群組中，展開 內該規則群組中，選取的規則 **核取方塊**前面規則編號，然後選取**停用**上述 索引標籤上。 若要變更動作類型的規則中的個別規則設定，選取規則數目，前面的核取方塊，然後選取**變更動作**上述 索引標籤。

 ![變更 WAF 規則集](./media/waf-front-door-create-portal/managed2.png)

## <a name="next-steps"></a>後續步驟

- 了解 [Azure Web 應用程式防火牆](waf-overview.md)。
- 深入了解[Azure 大門](front-door-overview.md)。
