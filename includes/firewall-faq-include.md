---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 7/19/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 1db5f0a62a21d040949c9f4e4c42f80c86e76506
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163674"
---
### <a name="what-is-azure-firewall"></a>何謂 Azure 防火牆？

Azure 防火牆是受控、雲端式網路安全性服務，可以保護您的 Azure 虛擬網路資源。 它是完全具狀態防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 您可以橫跨訂用帳戶和虛擬網路集中建立、強制執行以及記錄應用程式和網路連線原則。 Azure 防火牆目前為公開預覽狀態。

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Azure 防火牆公開預覽版本中支援哪些功能？  

* 具狀態防火牆即服務
* 內建高可用性且不受限制的雲端延展性
* FQDN 篩選 
* 網路流量篩選規則
* 輸出 SNAT 支援
* 跨 Azure 訂用帳戶和 VNET，集中建立、強制執行以及記錄應用程式和網路連線原則
* 與 Azure 監視器完全整合以進行記錄和分析 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>如何加入 Azure 防火牆公開預覽？

Azure 防火牆目前為受控的公開預覽版本，您可以使用 Register-AzureRmProviderFeature PowerShell 命令加入，如 Azure 防火牆公開預覽文件中所述。

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火牆的定價為何？

Azure 防火牆有固定成本和變動成本。 價格如下，而且在公開預覽期間，還會根據這些價格再打五折。

* 固定費用：每小時每個防火牆 1.25 美元
* 變動費用：防火牆處理的每 GB 0.03 美元 (輸入或輸出)。

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火牆的一般部署模型是什麼？

雖然您可以在任何 VNET 上部署 Azure 防火牆，但是客戶通常會在中央 VNET 部署 Azure 防火牆，並在中樞和支點模型中，將其他 VNET 對等互連到該 Azure 防火牆。 接著，您可以從對等互連的 VNET 設定預設路由，以指向此中央防火牆 VNET。

### <a name="how-can-i-install-the-azure-firewall"></a>如何安裝 Azure 防火牆？

您可以透過 Azure 入口網站、PowerShell、REST API 或範本，設定 Azure 防火牆。 如需逐步指示，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](../articles/firewall/tutorial-firewall-deploy-portal.md)。

### <a name="what-are-some-azure-firewall-concepts"></a>什麼是 Azure 防火牆概念？

Azure 防火牆支援規則和規則集合。 規則集合是一組共用相同順序和優先順序的規則。 規則集合會依其優先順序執行，網路規則集合的優先順序高於應用程式規則集合，所有規則都將終止。
規則集合有兩種類型：

* 應用程式規則：能夠讓您設定可以從子網路存取的完整網域名稱 (FQDN)。 
* 網路規則：可讓您設定包含來源位址、通訊協定、目的地連接埠，以及目的地位址的規則。 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火牆是否支援輸入流量篩選？

Azure 防火牆公開預覽僅支援輸出篩選。 非 HTTP/S 通訊協定的輸入保護 (例如：RDP、SSH、FTP) 是針對 Azure 防火牆 GA 暫時規劃的。  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Azure 防火牆支援何種記錄/分析？

Azure 防火牆會與 Azure 監視器整合，以檢視和分析防火牆記錄。 記錄可以傳送至 Log Analytics、Azure 儲存體或事件中樞。 這些記錄可以在 Log Analytics 中進行分析，或透過不同的工具 (例如 Excel 和 Power BI) 進行分析。 如需更多詳細資料，請參閱[教學課程：監視 Azure 防火牆記錄](../articles/firewall/tutorial-diagnostics.md)。

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>相對於現有類似 Marketplace 中的 NVA，Azure 防火牆如何運作？

Azure 防火牆是一種基本防火牆服務，可以處理特定的客戶案例。 我們希望客戶混合使用第三方 NVA 和 Azure 防火牆，並與我們的合作夥伴共同開展更多更好的合作機會。 
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>應用程式閘道 WAF 與 Azure 防火牆有什麼不同？

Web 應用程式防火牆 (WAF) 是應用程式閘道的一個功能，可提供 Web 應用程式的集中式輸入保護，免於遭遇常見的攻擊和弱點。 Azure 防火牆為所有連接埠和通訊協定提供輸出網路層級的保護，並為輸出 HTTP/S 提供應用程式層級的保護。 非 HTTP/S 通訊協定的輸入保護 (例如：RDP、SSH、FTP) 是針對 Azure 防火牆 GA 暫時規劃的。

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>網路安全性群組 (NSG) 和 Azure 防火牆有什麼不同？

Azure 防火牆服務可補足我們現有的網路安全性群組功能，結合在一起可提供更佳的深度防禦網路安全性。 NSG 提供分散式網路層流量篩選，以限制每個訂用帳戶中虛擬網路內資源的流量。  Azure 防火牆是完全具狀態的集中式網路防火牆即服務，可跨不同的訂用帳戶和虛擬網路 (VNet)，提供網路和應用程式層級的保護。 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用我的服務端點設定 Azure 防火牆？

若要安全存取 PaaS 服務，建議使用服務端點。 Azure 防火牆客戶可以選擇在 Azure 防火牆子網路中啟用服務端點，並且在連線的支點 VNET 上停用該服務端點，如此便可讓所有流量受益於服務端點安全性和集中式記錄這兩項功能。

### <a name="what-are-the-known-service-limits"></a>已知的服務限制有哪些？

* Azure 防火牆的軟性限制為每個月每個防火牆 1000 TB。 
* 在中央 VNET 中執行的 Azure 防火牆受限於 VNET 對等互連限制：最多 50 個支點 VNET。  
* Azure 防火牆無法搭配全域對等互連使用，因此客戶應該每個區域至少部署一個防火牆。