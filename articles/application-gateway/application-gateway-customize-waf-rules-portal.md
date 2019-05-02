---
title: 自定义 Azure 应用程序网关的 Web 应用程序防火墙规则 - Azure 门户
description: 本文提供如何透過 Azure 入口網站，在應用程式閘道中自訂 Web 應用程式防火牆規則的相關資訊。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f7ffb8d6adfd4afc75618834a3fe82cf9a3d0c9f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720391"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-portal"></a>透過 Azure 入口網站自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 (WAF) 提供 Web 應用程式的保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 如需特定規則群組與規則的詳細資訊，請參閱 [Web 應用程式防火牆 CRS 規則群組與規則的清單](application-gateway-crs-rulegroups-rules.md)。

>[!NOTE]
> 如果您的應用程式閘道並未使用 WAF 層，右窗格中會出現將應用程式閘道升級至 WAF 層的選項。 

![啟用 WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

**檢視規則群組與規則**
   1. 瀏覽至應用程式閘道，然後選取 [Web 應用程式防火牆]。  
   2. 選取 [進階規則設定]。  
   這個檢視會在透過所選擇規則集提供之所有規則群組的頁面上顯示一個表格， 並選取所有的規則核取方塊。

![設定已停用的規則][1]

## <a name="search-for-rules-to-disable"></a>搜尋要停用的規則

**Web 應用程式防火牆設定**頁面提供的功能，來篩選透過文字搜尋的規則。 結果只會顯示包含您搜尋之文字的規則群組與規則。

![搜尋規則][2]

## <a name="disable-rule-groups-and-rules"></a>停用規則群組與規則

> [!IMPORTANT]
> 停用任何規則群組] 或 [規則時，請務必小心。 這可能會您暴露於更高的安全性風險。

禁用规则时可以禁用整个规则组，也可以禁用一个或多个规则组下的特定规则。 

**停用規則群組或特定規則**

   1. 搜尋您要停用的規則或規則群組。
   2. 清除您要停用之規則的核取方塊。 
   2. 選取 [ **儲存**]。 

![儲存變更][3]

## <a name="mandatory-rules"></a>强制性规则

下列清單包含會造成封鎖的要求在防止模式中 WAF 的條件。 在偵測模式中，它們會記錄為例外狀況。

无法配置或禁用这些规则：

* 除非关闭正文检查（XML、JSON、表单数据），否则无法分析请求正文会导致请求被阻止
* 请求正文（不带文件）数据长度大于配置的限制
* 请求正文（包括文件）大于限制
* WAF 引擎发生内部错误

CRS 3.x 特定：

* 入站异常分数超出阈值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
