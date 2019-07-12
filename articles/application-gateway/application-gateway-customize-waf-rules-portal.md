---
title: 自訂 Azure 應用程式閘道-Azure 入口網站中的 web 應用程式防火牆規則
description: 本文提供如何透過 Azure 入口網站，在應用程式閘道中自訂 Web 應用程式防火牆規則的相關資訊。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: f4af52907ab2e950636dea0874b49500f3a6b587
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613449"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 (WAF) 提供 Web 應用程式的保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 如需特定規則群組與規則的詳細資訊，請參閱 [Web 應用程式防火牆 CRS 規則群組與規則的清單](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果您的應用程式閘道並未使用 WAF 層，右窗格中會出現將應用程式閘道升級至 WAF 層的選項。 

![啟用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

**檢視規則群組與規則**
   1. 瀏覽至應用程式閘道，然後選取 [Web 應用程式防火牆]  。  
   2. 選取 [進階規則設定]  。  
   這個檢視會在透過所選擇規則集提供之所有規則群組的頁面上顯示一個表格， 並選取所有的規則核取方塊。

![設定已停用的規則][1]

## <a name="search-for-rules-to-disable"></a>搜尋要停用的規則

**Web 應用程式防火牆設定**頁面提供的功能，來篩選透過文字搜尋的規則。 結果只會顯示包含您搜尋之文字的規則群組與規則。

![搜尋規則][2]

## <a name="disable-rule-groups-and-rules"></a>停用規則群組與規則

> [!IMPORTANT]
> 停用任何規則群組] 或 [規則時，請務必小心。 這可能會您暴露於更高的安全性風險。

當您停用規則時，您可以停用整個規則群組] 或 [一或多個規則群組下的特定規則。 

**停用規則群組或特定規則**

   1. 搜尋您要停用的規則或規則群組。
   2. 清除您要停用之規則的核取方塊。 
   2. 選取 [ **儲存**]。 

![儲存變更][3]

## <a name="mandatory-rules"></a>必要的規則

下列清單包含會造成封鎖的要求在防止模式中 WAF 的條件。 在偵測模式中，它們會記錄為例外狀況。

無法設定或停用這些：

* 無法剖析要求主體導致要求遭到封鎖，除非主體檢查已關閉 （XML、 JSON、 表單資料）
* 要求本文中 （的任何檔案） 的資料長度大於設定的限制
* （包括檔案） 的內文大於限制的要求
* WAF 引擎中發生內部錯誤

CRS 3.x 特定：

* 輸入異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
