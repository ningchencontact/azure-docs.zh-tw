---
title: 設定多個 Ise 的存取權
description: 針對多個整合服務環境（Ise），您可以設定單一公用輸出 IP 位址，以從 Azure Logic Apps 存取外部系統
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f3b422a55b7e2abbc8b1538183fd57fb234900d4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792695"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>在 Azure Logic Apps 中設定多個整合服務環境的存取權

當您使用 Azure Logic Apps 時，您可以設定[*整合服務環境*（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)來裝載需要存取[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)中資源的邏輯應用程式。 如果您有多個 ISE 實例需要存取具有 IP 限制的其他端點，請將[Azure 防火牆](../firewall/overview.md)或[網路虛擬裝置](../virtual-network/virtual-networks-overview.md#filter-network-traffic)部署至您的虛擬網路，並透過該防火牆或網路虛擬裝置來路由輸出流量。 接著，您可以讓虛擬網路中的所有 ISE 實例使用單一、可預測的公用 IP 位址與目的地系統進行通訊。 如此一來，您就不需要在目的地系統上為每個 ISE 設定額外的防火牆開頭。 本主題說明如何透過 Azure 防火牆路由輸出流量，但您可以將類似的概念套用至虛擬網路虛擬裝置，例如 Azure Marketplace 的協力廠商防火牆。

## <a name="prerequisites"></a>必要條件

* 在與 ISE 相同的虛擬網路中執行的 Azure 防火牆。 如果您沒有防火牆，請先將名為 `AzureFirewallSubnet` 的[子網新增](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)至您的虛擬網路。 接著，您可以在虛擬網路中[建立及部署防火牆](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

* Azure[路由表](../virtual-network/manage-route-table.md)。 如果您沒有帳戶，請先[建立一個路由表](../virtual-network/manage-route-table.md#create-a-route-table)。 如需路由的詳細資訊，請參閱[虛擬網路流量路由](../virtual-network/virtual-networks-udr-overview.md)。

## <a name="set-up-route-table"></a>設定路由表

1. 在  [Azure 入口網站](https://portal.azure.com)中，選取 [路由表]，例如：

   ![選取路由表與規則以引導輸出流量](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. 若要[新增路由](../virtual-network/manage-route-table.md#create-a-route)，請在 [路由表] 功能表上，選取 [**路由**] > **新增**。

   ![新增路由以引導輸出流量](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. 在 [**新增路由**] 窗格中，使用指定目的地系統的所有傳出流量遵循此行為的規則來[設定新的路由](../virtual-network/manage-route-table.md#create-a-route)：

   * 使用[**虛擬裝置**](../virtual-network/virtual-networks-udr-overview.md#user-defined)做為下一個躍點類型。

   * 移至防火牆實例的私人 IP 位址做為下一個躍點位址。

     若要尋找此 IP 位址，請在您的防火牆功能表上，選取 **[總覽**]，尋找 [**私人 IP 位址**] 底下的位址，例如：

     ![尋找防火牆私人 IP 位址](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   以下範例會顯示這類規則的外觀：

   ![設定規則以引導輸出流量](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | 屬性 | Value | 描述 |
   |----------|-------|-------------|
   | **路由名稱** | <*唯一的路由名稱*> | 路由表中路由的唯一名稱 |
   | **位址前置詞** | <*目的地位址*> | 您想要流量前往的目的地系統位址。 請確定您針對此位址使用無[類別網域間路由（CIDR）標記法](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)。 |
   | **下一個躍點類型** | **虛擬裝置** | 輸出流量所使用的[躍點類型](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **下一個躍點位址** | <*防火牆-私人 IP 位址*> | 防火牆的私人 IP 位址 |
   |||

## <a name="set-up-network-rule"></a>設定網路規則

1. 在 Azure 入口網站中，尋找並選取您的防火牆。 在 [防火牆] 功能表的 [**設定**] 底下，選取 [**規則**]。 在 [規則] 窗格中，選取 [**網路規則集合**] > [**新增網路規則集合**]。

   ![新增網路規則集合至防火牆](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. 在集合中，新增規則以允許目的地系統的流量。

   例如，假設您有一個在 ISE 中執行的邏輯應用程式，而且需要與 SFTP 系統通訊。 您會建立名為 `LogicApp_ISE_SFTP_Outbound`的網路規則集合，其中包含名為 `ISE_SFTP_Outbound`的網路規則。 此規則允許使用您的防火牆私人 IP 位址，從您的 ISE 在虛擬網路中執行的任何子網的 IP 位址，到目的地 SFTP 系統的流量。

   ![設定防火牆的網路規則](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **網路規則集合屬性**

   | 屬性 | Value | 描述 |
   |----------|-------|-------------|
   | **名稱** | <*網路-規則-集合-名稱*> | 網路規則集合的名稱 |
   | **優先順序** | <*優先權層級*> | 要用來執行規則集合的優先順序順序。 如需詳細資訊，請參閱[什麼是 Azure 防火牆的概念](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)？ |
   | **Action** | **允許** | 要為此規則執行的動作類型 |
   |||

   **網路規則屬性**

   | 屬性 | Value | 描述 |
   |----------|-------|-------------|
   | **名稱** | <*的網路-規則-名稱*> | 網路規則的名稱 |
   | **通訊協定** | <*連接-通訊協定*> | 要使用的連接通訊協定。 例如，如果您使用 NSG 規則，請同時選取 [ **tcp** ] 和 [ **UDP**]，而不是 [ **tcp**]。 |
   | **來源位址** | <*ISE-子網位址*> | 您的 ISE 執行所在的子網 IP 位址，以及來自邏輯應用程式的流量來源 |
   | **目的地位址** | <*目的地-IP 位址*> | 您想要讓流量前往的目的地系統 IP 位址 |
   | **目的地埠** | <*目的地-埠*> | 您的目的地系統用來進行輸入通訊的任何埠 |
   |||

   如需網路規則的詳細資訊，請參閱下列文章：

   * [設定網路規則](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure 防火牆規則處理邏輯](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure 防火牆常見問題](../firewall/firewall-faq.md)
   * [Azure PowerShell：新增-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI： az network firewall network-rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>後續步驟

* [從 Azure Logic Apps 連接到 Azure 虛擬網路](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)