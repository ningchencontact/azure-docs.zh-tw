---
title: 設定 Azure HDInsight 叢集的輸出網路流量限制
description: 了解如何設定 Azure HDInsight 叢集的輸出網路流量限制。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: af5ddd50556b493cddf27d1ebb766d9bf6105107
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433440"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>設定 Azure HDInsight 叢集使用的防火牆 （預覽） 的輸出網路流量

這篇文章提供您保護輸出流量從您的 HDInsight 叢集使用 Azure 防火牆的步驟。 下列步驟假設您要設定現有叢集的 Azure 防火牆。 如果您要部署新的叢集，而且在防火牆後面，先建立您的 HDInsight 叢集和子網路，並依照本指南中的步驟。

## <a name="background"></a>背景

Azure HDInsight 叢集通常會在您自己的虛擬網路中部署。 叢集中有該虛擬網路外部網路存取，才能正確運作所需的服務上的相依性。

有數個需要的輸入的流量的相依性。 輸入的管理流量不能透過防火牆裝置傳送。 此流量的來源位址已知且發行[此處](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)。 您也可以建立網路安全性群組 (NSG) 規則，與這項資訊來保護叢集的輸入的流量。

使用 Fqdn，不需要其背後的靜態 IP 位址幾乎已完全定義 HDInsight 輸出流量相依性。 靜態位址缺乏表示網路安全性群組 (Nsg)，不能用來鎖定從叢集中的輸出流量。 其中一個不能用來設定目前的名稱解析為基礎的規則，然後使用它來設定 NSG 規則，則位址會變更通常足以。

保護輸出位址的解決方案是使用可以控制根據網域名稱的輸出流量的防火牆裝置。 Azure 防火牆可以限制輸出的目的地 FQDN 為基礎的 HTTP 和 HTTPS 流量或[FQDN 標記](https://docs.microsoft.com/azure/firewall/fqdn-tags)。

## <a name="configuring-azure-firewall-with-hdinsight"></a>使用 HDInsight 中設定 Azure 防火牆

鎖住您現有的 HDInsight 與 Azure 防火牆的輸出的步驟摘要如下：
1. 建立防火牆規則。
1. 加入至防火牆的應用程式規則
1. 將網路規則新增至防火牆。
1. 建立路由表。

### <a name="create-a-new-firewall-for-your-cluster"></a>建立新的防火牆，為您的叢集

1. 建立名為的子網路**AzureFirewallSubnet**叢集所在的虛擬網路中。 
1. 建立新的防火牆**測試 FW01**中的步驟[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)。

### <a name="configure-the-firewall-with-application-rules"></a>設定防火牆的應用程式規則

建立可讓叢集以傳送和接收重要通訊的應用程式規則集合。

選取新的防火牆**測試 FW01**從 Azure 入口網站。 按一下 **規則**下方**設定** > **應用程式規則集合** > **新增應用程式規則集合**.

![標題：新增應用程式規則集合](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

在 **新增應用程式規則集合**畫面上，完成下列步驟：

1. 輸入**名稱**，**優先順序**，然後按一下**允許**從**動作**下拉式功能表中，並輸入下列規則在**FQDN 標記區段**:

   | **名稱** | **來源位址** | **FQDN 標記** | **注意事項** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight 和 WindowsUpdate | 所需的 HDI 服務 |

1. 新增下列規則，以便**目標 Fqdn 區段**:

   | **名稱** | **來源位址** | **Protocol:Port** | **目標 FQDN** | **注意事項** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | 可讓 Windows 登入活動 |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | 如果您的叢集做為後盾 WASB，然後新增規則的 WASB。 若要使用僅限 https 連線確定[「 需要安全傳輸 」](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)啟用儲存體帳戶。 |

1. 按一下 [新增]  。

   ![標題：輸入應用程式規則集合的詳細資料](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>設定防火牆與網路規則

建立網路規則，以正確地設定您的 HDInsight 叢集。

1. 選取新的防火牆**測試 FW01**從 Azure 入口網站。
1. 按一下 **規則**下方**設定** > **網路規則集合** > **新增網路規則集合**。
1. 在上**新增網路規則集合**畫面上，輸入**名稱**，**優先順序**，然後按一下**允許**從**動作**下拉式功能表中。
1. 建立中的下列規則**IP 位址**區段：

   | **名稱** | **通訊協定** | **來源位址** | **目的地位址** | **目的地連接埠** | **注意事項** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | 時間服務 |
   | Rule_2 | 任意 | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | 如果您使用企業安全性套件 」 (ESP)，然後可允許與 AAD DS ESP 叢集進行通訊的 IP 位址區段中新增網路規則。 您可以在入口網站中找到 AAD DS 區段上的網域控制站的 IP 位址 | 
   | Rule_3 | TCP | * | 您的 Data Lake 儲存體帳戶的 IP 位址 | `*` | 如果您使用 Azure Data Lake 儲存體，您可以解決 ADLS sku:gen1 和 Gen2 SNI 問題的 IP 位址 區段中新增網路規則。 此選項會將流量路由傳送到防火牆，可能會導致較高的成本，對於大型資料載入，但流量將會進行記錄，並可稽核防火牆記錄檔中。 判斷您的 Data Lake 儲存體帳戶的 IP 位址。 您可以使用 powershell 命令，例如`[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`來將 FQDN 解析到 IP 位址。|
   | Rule_4 | TCP | * | * | `12000` | （選擇性）如果您使用 Log Analytics，然後與您的 Log Analytics 工作區進行通訊的 IP 位址 區段中建立網路規則。 |

1. 建立中的下列規則**服務標籤**區段：

   | **名稱** | **通訊協定** | **來源位址** | **服務標記** | **目的地連接埠** | **注意事項** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | 在 [服務標記] 區段中設定網路規則，可讓您記錄和稽核 SQL 流量，除非您對 SQL Server 設定服務端點將會略過防火牆之 HDInsight 子網路上的 sql。 |

1. 按一下 **新增**以完成建立網路規則集合。

   ![標題：輸入應用程式規則集合的詳細資料](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>建立及設定路由表

使用下列項目中建立路由表：

1. 從六個位址範圍[這份必要的 HDInsight 管理 IP 位址](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip)使用的下一個躍點**網際網路**:
    1. 在所有區域中的所有叢集的四個 IP 位址
    1. 兩個都有專用的叢集建立所在的區域的 IP 位址
1. 正在您的 Azure 防火牆私人 IP 位址的下一個躍點 IP 位址 0.0.0.0/0 的一個虛擬設備路由。

比方說，若要設定的路由表中的 「 美國中部 」 美國地區建立的叢集，請使用下列步驟：

1. 登入 Azure 入口網站。
1. 選取您的 Azure 防火牆**測試 FW01**。 複製**私人 IP 位址**上列出**概觀**頁面。 此範例中我們將使用**範例 10.1.1.4 位址**
1. 建立新的路由表。
1. 按一下 **路由**下方**設定**。
1. 按一下 **新增**下表中建立的 IP 位址的路由。

| 路由名稱 | 位址首碼 | 下一個躍點類型 | 下一個躍點位址 |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.67.223.215 | 13.67.223.215/32 | Internet | NA |
| 40.86.83.253 | 40.86.83.253/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | 虛擬設備 | 10.1.1.4 |

完成將路由表的組態：

1. 指派您按一下 建立與您的 HDInsight 子網路的路由表**子網路**下方**設定**，然後**關聯**。
1. 在上**子網路建立關聯**畫面上，選取您的叢集建立到虛擬網路並**HDInsight 子網路**您使用於您的 HDInsight 叢集。
1. 按一下 [確定]  。

## <a name="edge-node-or-custom-application-traffic"></a>邊緣節點或自訂應用程式流量

上述的步驟可讓叢集操作，而不發生問題。 您仍然需要設定相依性，以便容納在邊緣節點上執行的自訂應用程式，如果適用的話。

必須識別應用程式相依性，並將它加入 Azure 防火牆或路由表中。

應用程式流量，以避免非對稱式路由問題，就必須建立路由。

如果您的應用程式有其他相依性，它們必須新增至您的 Azure 防火牆。 建立應用程式規則來允許 HTTP/HTTPS 流量，以及建立網路規則來允許其他一切流量。

## <a name="logging"></a>記錄

Azure 防火牆可以將記錄傳送至一些不同的儲存體系統中。 如需設定指示記錄您的防火牆，請遵循中的步驟[教學課程：監視 Azure 防火牆記錄檔和度量](../firewall/tutorial-diagnostics.md)。

一旦您完成記錄設定 中，如果您是資料記錄到 Log Analytics，您可以檢視封鎖的流量使用的查詢，如下所示：

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

第一次取得 應用程式，運作時您不知道的所有應用程式相依性存在時，與 Azure 監視器記錄檔整合您的 Azure 防火牆很有用的。 您可以從[分析 Azure 監視器中的記錄資料](../azure-monitor/log-query/log-query-overview.md)深入了解 Azure 監視器記錄

## <a name="access-to-the-cluster"></a>叢集的存取權
已成功有防火牆安裝程式之後，您可以使用內部端點 (`https://<clustername>-int.azurehdinsight.net`) 來存取 VNET 中的從 Ambari。 若要使用的公用端點 (`https://<clustername>.azurehdinsight.net`) 或 ssh 端點 (`<clustername>-ssh.azurehdinsight.net`)，請確定您有正確的路由的路由表中，而且 NSG 規則設定，以避免對稱路由問題說明[這裡](https://docs.microsoft.com/azure/firewall/integrate-lb)。

## <a name="configure-another-network-virtual-appliance"></a>設定另一個網路虛擬設備

>[!Important]
> 下列資訊**只**需要如果您想要設定 Azure 防火牆以外的網路虛擬設備 (NVA)。

前面的指示可協助您設定 Azure 防火牆來限制輸出流量從您的 HDInsight 叢集。 若要允許流量的許多常見的重要案例，會自動設定 azure 防火牆。 如果您想要使用另一個網路虛擬設備，您必須手動設定一些其他功能。 記住下列為您設定您的網路虛擬設備：

* 應使用服務端點來設定支援的服務端點服務。
* IP 位址相依性僅適用於非 HTTP/S 流量 （TCP 和 UDP 流量）。
* FQDN HTTP/HTTPS 端點可以放在您的 NVA 裝置中。
* 而異的限定詞編號為基礎的相依性萬用字元 HTTP/HTTPS 端點。
* 指派您建立與您的 HDInsight 子網路的路由表。

### <a name="service-endpoint-capable-dependencies"></a>服務端點功能相依性

| **端點** |
|---|
| Azure SQL |
| Azure 儲存體 |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP 位址相依性

| **端點** | **詳細資料** |
|---|---|
| \*:123 | NTP 時鐘檢查。 在連接埠 123 上的多個端點檢查流量 |
| Ip 發行[這裡](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | 這些是 HDInsight 服務 |
| AAD DS 私人 Ip 為 ESP 叢集 |
| \*: 16800 KMS Windows 啟用 |
| \*12000 for Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 相依性

>[!Important]
> 下列清單只會提供幾個最重要的 Fqdn。 您可以取得完整的 Fqdn 清單來設定您的 NVA[此檔案中](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)。

| **端點**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 虛擬網路架構](hdinsight-virtual-network-architecture.md)
