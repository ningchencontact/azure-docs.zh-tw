---
title: "使用 Azure Site Recovery 複寫多層式 IIS 型 web 應用程式 | Microsoft Docs"
description: "本文說明如何使用 Azure Site Recovery 複寫 IIS web 伺服陣列虛擬機器。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 00d5c1fa8c0c16daef5d928147e169553672e1f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>使用 Azure Site Recovery 複寫多層式 IIS 型 web 應用程式

## <a name="overview"></a>概觀


應用程式軟體是組織中商務產能的引擎。 各種 web 應用程式可在組織提供不同的用途。 這當中有些 (例如薪資處理、財務應用程式及面對客戶的網站) 可能對組織來說是最重要的。 對組織來說，讓這些應用程式維持隨時運作以避免產能損失相當重要，但更重要的是避免對公司品牌形象造成任何損害。

重要的 Web 應用程式通常會設定為多層式應用程式，其中 Web、資料庫及應用程式分別位於不同層上。 除了會分散在各層外，應用程式也可在每一層中使用多部伺服器中以負載平衡流量。 此外，各層之間和 web 伺服器上的對應可能會根據靜態 IP 位址。 在容錯移轉時，部分對應將需要更新，尤其是如果您在 Web 伺服器上設定了多個網站。 如果 Web 應用程式使用 SSL，就必須更新憑證繫結。

傳統的非複寫型復原方法牽涉到備份各種組態檔、登錄設定、繫結、自訂元件 (COM 或 .NET)、內容，還有憑證，以及透過一組手動步驟來復原檔案。 這些技術顯然都很麻煩、容易出錯且無法調整。 例如，您很容易忘了備份憑證，且在容錯移轉之後不得不購買伺服器的新憑證。

理想的災害復原解決方案應允許以複雜的應用程式架構為中心，建立復原方案模型。 它也應該要能夠新增自訂步驟，以處理各層之間的應用程式對應。 如果發生災害，這將能夠提供單鍵準確命中的解決方案，進而縮短 RTO。


本文說明如何使用 [Azure Site Recovery](site-recovery-overview.md) 來保護 IIS 型 Web 應用程式。 本文涵蓋將三層式 IIS 型 Web 應用程式複寫至 Azure 的最佳做法、如何進行災害復原演練，以及如何將應用程式容錯移轉至 Azure。


## <a name="prerequisites"></a>先決條件

開始之前，請確定您了解下列需求︰

1. [將虛擬機器複寫至 Azure](site-recovery-vmware-to-azure.md)
1. 如何[設計修復網路](site-recovery-network-design.md)
1. [執行測試容錯移轉至 Azure](./site-recovery-test-failover-to-azure.md)
1. [執行容錯移轉至 Azure](site-recovery-failover.md)
1. 如何[複寫網域控制站](site-recovery-active-directory.md)
1. 如何[複寫 SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>部署模式
IIS 型 web 應用程式通常會遵循下列其中一個部署模式︰

**部署模式 1**具有「應用程式要求路由」(ARR)、「IIS 伺服器」及 Microsoft SQL Server 的 IIS 型 Web 伺服陣列。

![部署模式](./media/site-recovery-iis/deployment-pattern1.png)

**部署模式 2** 具有應用程式要求路由 (ARR)、IIS 伺服器、應用程式伺服器與 Microsoft SQL Server 的 IIS 型 web 伺服陣列。


![部署模式](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery 支援

針對本文的建立目的，將會使用 Windows Server 2012 R2 Enterprise 上有 IIS 伺服器 7.5 版的 VMware 虛擬機器。 因為站台復原複寫應用程式無從驗證，此處提供的建議也都必須保留下列案例，以及其他版本的 IIS。

### <a name="source-and-target"></a>來源與目標

**案例** | **至次要網站** | **至 Azure**
--- | --- | ---
**Hyper-V** | yes | yes
**VMware** | yes | yes
**實體伺服器** | 否 | yes
**Azure**|NA|yes

## <a name="replicate-virtual-machines"></a>複寫虛擬機器

請依照[本指引](site-recovery-vmware-to-azure.md)開始將所有 IIS web 伺服陣列虛擬機器複寫至 Azure。

如果您是使用靜態 IP，則請在 [計算] 和 [網路] 設定的 [[**目標 IP**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties)] 設定中，指定您希望虛擬機器採用的 IP。

![目標 IP](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>建立復原計劃

復原計劃可讓您在多層式應用程式中排序各層的容錯移轉，因此可維護應用程式一致性。 以下是為多層式 Web 應用程式建立復原方案的步驟。  [深入了解如何建立復原計劃](./site-recovery-create-recovery-plans.md)。

### <a name="adding-virtual-machines-to-failover-groups"></a>將虛擬機器新增至容錯移轉群組
一般多層式 IIS Web 應用程式包含具有 SQL 虛擬機器的資料庫層、由 IIS 伺服器所構成的 Web 層，以及應用程式層。 請根據下列步驟中指定的階層，將所有這些虛擬機器新增至不同的群組。 [深入了解自訂復原方案](site-recovery-runbook-automation.md#customize-the-recovery-plan)。

1. 建立復原計畫。 在群組 1 下新增資料庫層虛擬機器以確保它們都後關閉並先提供。

1. 在群組 2 下新增應用程式層虛擬機器，以致於提供資料庫層之後，它們會予以提供。

1. 在群組 3 中新增 web 層虛擬機器，以致於提供應用程式層之後，它們會予以提供。

1. 在群組 4 中新增虛擬機器負載平衡，以致於提供 web 層之後，它們會予以提供。


### <a name="adding-scripts-to-the-recovery-plan"></a>將指令碼新增至復原計畫
您可能需要在容錯移轉/測試容錯移轉後於 Azure 虛擬機器上執行某些作業以讓 IIS web 伺服陣列正確運作。 您可以自動化後置容錯移轉作業，例如更新 DNS 項目、變更網站繫結、在如下所示的復原方案中新增對應指令碼在連接字串中進行變更。 [深入了解新增指令碼復原計劃](./site-recovery-how-to-add-vmmscript.md)。

#### <a name="dns-update"></a>DNS 更新
如果已設定動態 DNS 更新的 DNS，則虛擬機器通常會在啟動之後使用新的 IP 更新 DNS。 如果您想要新增一個明確的步驟來以虛擬機器的新 IP 更新 DNS，則請新增此[指令碼來更新 DNS 中的 IP](https://aka.ms/asr-dns-update)，以作為復原方案群組上的後置動作。  

#### <a name="connection-string-in-an-applications-webconfig"></a>在應用程式 web.config 中的連接字串
連接字串會指定與網站進行通訊的資料庫。

如果連接字串含有資料庫虛擬機器的名稱，則容錯移轉後無須採取任何進一步的步驟。 應用程式可以自動對資料庫進行通訊。 此外，如果保留資料庫虛擬機器的 IP 位址，則它不需要更新連接字串。 如果連接字串使用 IP 位址來參考資料庫虛擬機器，則在容錯移轉後就必須更新連接字串。 例如，以下連接字串會指向 IP 為 127.0.1.2 的資料庫

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

您可以更新 web 層中的連接字串，方法為在復原計劃中的群組 3 之後新增 [IIS 連線更新指令碼](https://aka.ms/asr-update-webtier-script-classic)。

#### <a name="site-bindings-for-the-application"></a>應用程式的網站繫結
每個網站所包含的繫結資訊包括繫結的類型、IIS 伺服器接聽網站要求的 IP 位址、連接埠號碼，以及網站的主機名稱。 在容錯移轉期間，如果與這些繫結關聯的 IP 位址發生變更，可能會需要更新這些繫結。

> [!NOTE]
>
> 如果您已針對網站繫結標記「全部未指派」 (如以下範例所示)，在容錯移轉後，就不需要更新此繫結。 此外，如果與網站相關聯的 IP 位址在容錯移轉後未變更，則網站繫結不需要更新 (IP 位址保留取決於指派至主要和復原網站的網路架構和子網路，因此對您的組織可能可行或可能不可行。)

![SSL 繫結](./media/site-recovery-iis/sslbinding.png)

如果您已將 IP 位址與網站相關聯，必須使用新的 IP 位址更新所有網站繫結。 您可以在復原計劃中的群組 3 之後新增 [IIS Web 層更新指令碼](https://aka.ms/asr-web-tier-update-runbook-classic)以變更網站繫結。


#### <a name="update-load-balancer-ip-address"></a>更新負載平衡器 IP 位址
如果您有應用程式要求路由虛擬機器，請在群組 4 之後新增 [IIS ARR 容錯移轉指令碼](https://aka.ms/asr-iis-arrtier-failover-script-classic)以更新 IP 位址。

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Https 連線的 SSL 憑證繫結
網站可擁有相關聯的 SSL 憑證，以協助確保 web 伺服器與使用者瀏覽器之間的安全通訊。 如果網站具有 https 連線，而關聯的 https 網站繫結以 SSL 憑證繫結來繫結至 IIS 伺服器 IP 位址，則在容錯移轉後，必須為該憑證新增一個具有 IIS 虛擬機器 IP 的新網站繫結。

可以發出 SSL 憑證，針對-

a) 網站的完整網域名稱<br>
b) 伺服器的名稱<br>
c) 網域名稱的萬用字元憑證<br>
d) IP 位址 – 如果針對 IIS 伺服器的 IP 簽發 SSL 憑證，就必須針對 Azure 網站上 IIS 伺服器的 IP 位址簽發另一個 SSL 憑證，並且必須為此憑證建立額外 SSL 繫結。 因此，建議您不要使用針對 IP 簽發的 SSL 憑證。 此選項較不常用，而且很快就會依據新的 CA/瀏覽器論壇變更而被取代。

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>更新 web 和應用程式層之間的相依性
如果您有依據虛擬機器 IP 位址變動的應用程式特定相依性，就必須在容錯移轉後更新此相依性。

## <a name="doing-a-test-failover"></a>執行測試容錯移轉
請依照[本指引](site-recovery-test-failover-to-azure.md)來執行測試容錯移轉。

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
1.  按一下為 IIS web 伺服器陣列建立的復原計劃。
1.  按一下 [測試容錯移轉]。
1.  選取復原點和 Azure 虛擬網路來開始測試容錯移轉程序。
1.  次要環境啟動後，您就可以執行您的驗證。
1.  驗證完成後，您可以選取 [驗證完成]，系統就會清理測試容錯移轉環境。

## <a name="doing-a-failover"></a>執行容錯移轉
當您在進行容錯移轉時，請依照[本指引](site-recovery-failover.md)。

1.  請移至 Azure 入口網站，然後選取您的復原服務保存庫。
1.  按一下為 IIS web 伺服器陣列建立的復原計劃。
1.  按一下 [容錯移轉]。
1.  選取復原點以啟動容錯移轉程序。

## <a name="next-steps"></a>後續步驟
您可以使用站台復原深入了解[複寫其他應用程式](site-recovery-workload.md)。
