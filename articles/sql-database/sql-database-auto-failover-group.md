---
title: 容錯移轉群組
description: 自動容錯移轉群組是 SQL Database 功能，允許您管理 SQL Database 伺服器上的一組資料庫，或受控執行個體中的所有資料庫的複寫和自動/協調容錯移轉。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421381"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自動容錯移轉群組可以啟用多個資料庫透明且協調的容錯移轉

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. 您可以手動起始容錯移轉，也可以根據使用者定義的原則將其委派給 SQL Database 服務。 後項可讓您在導致主要區域中完全或部分喪失 SQL Database 服務可用性的嚴重失敗或其他非計劃事件之後，自動復原次要地區中的多個相關資料庫。 A failover group can include one or multiple databases, typically used by the same application. 此外，您可以使用可讀取次要資料庫來卸載唯讀查詢工作負載。 因為自動容錯移轉群組牽涉到多個資料庫，所以這些資料庫必須在主要伺服器上進行設定。 自動容錯移轉群組支援將群組中的所有資料庫都只複寫到不同區域的一部次要伺服器。

> [!NOTE]
> 在 SQL Database 伺服器上使用單一或集區資料庫時，若您希望在相同或不同區域中有多個次要資料庫，請使用[主動式異地複寫](sql-database-active-geo-replication.md)。 

當您使用自動容錯移轉群組與自動容錯移轉原則時，影響群組中一或多個資料庫的任何中斷，都會導致自動容錯移轉。 Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

此外，自動容錯移轉群組還提供在容錯移轉期間仍保持不變的讀寫和唯讀接聽程式端點。 無論您使用手動或自動啟動容錯移轉，容錯移轉都會將群組中所有次要資料庫切換到主要資料庫。 資料庫容錯移轉完成後，DNS 記錄會自動更新以將端點重新導向至新的區域。 針對特定的 RPO 和 RTO 資料，請參閱[商務持續性概觀](sql-database-business-continuity.md)。

當您使用自動容錯移轉群組與自動容錯移轉原則時，任何影響 SQL Database 伺服器或受控執行個體中資料庫的中斷，都會導致自動容錯移轉。 您可以使用下列方法管理自動容錯移轉群組：

- [Azure 入口網站](sql-database-implement-geo-distributed-database.md)
- [PowerShell：容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API：容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups)

容錯移轉之後，請確認已在新的主要資料庫上設定伺服器和資料庫的驗證需求。 如需詳細資訊，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。

若要達到真正的業務持續性，新增資料中心之間的資料庫備援只是解決方案的一部分。 在災難性失敗後要端對端復原應用程式 (服務) 需要復原構成服務的所有元件和任何相依的服務。 這些元件的範例包括用戶端軟體 (例如自訂 JavaScript 的瀏覽器)、web 前端、儲存體和 DNS。 所有元件都必須對相同的失敗具有恢復功能，並且在應用程式的復原時間目標 (RTO) 內可供使用。 因此，您需要識別所有相依服務並了解其提供的保證與功能。 然後，您必須採取適當步驟以確保服務功能在它所依賴的服務容錯移轉期間都正常。 如需有關設計災害復原解決方案的詳細資訊，請參閱[使用主動式異地複寫設計災害復原的雲端解決方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="auto-failover-group-terminology-and-capabilities"></a>自動容錯移轉群組術語和功能

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **SQL Database 伺服器**

     使用 SQL Database 伺服器，可以將單一伺服器上的部分或所有使用者資料庫放在容錯移轉群組中。 此外，SQL Database 伺服器在單一 SQL Database 伺服器上可支援多個容錯移轉群組。

- **主要**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **次要**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. 次要資料庫和主要資料庫不能位於相同區域。

- **將單一資料庫新增至容錯移轉群組**

  您可以將多個在相同 SQL Database 伺服器上的單一資料庫放入相同的容錯移轉群組。 如果您在容錯移轉群組中新增單一資料庫，它會使用相同的版本自動建立次要資料庫，並在次要伺服器上計算大小。  當建立容錯移轉群組時，您會指定該伺服器。 如果您新增在次要伺服器中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增在伺服器中已經有次要資料庫但不屬於容錯移轉群組一部分的資料庫時，會在次要伺服器中建立新的次要資料庫。

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. 您無法在容錯移轉群組中選擇要複寫的使用者資料庫子集。

- **將彈性集區中的資料庫新增到容錯移轉群組**

  您可以將彈性集區中的所有或多個資料庫放入相同的容錯移轉群組。 如果主要資料庫在彈性集區中，則會使用相同名稱在該彈性集區中建立次要資料庫 (次要集區)。 您必須確保次要伺服器包含名稱完全相同的彈性集區，且有足夠的可用容量裝載容錯移轉群組將建立的次要資料庫。 如果您在集區中新增在次要集區中已經有次要資料庫的資料庫，群組就會繼承該異地複寫連結。 當您新增的資料庫在伺服器中已經有次要資料庫且不屬於容錯移轉群組一部分時，系統會在次要集區中建立新的次要資料庫。
  
- **DNS 區域**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **容錯移轉群組讀寫接聽程式**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **容錯移轉群組唯讀接聽程式**

  形成的 DNS CNAME 記錄指向唯讀接聽程式 (指向次要 URL)。 It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **自動容錯移轉原則**

  容錯移轉群組預設是利用自動容錯移轉原則設定。 SQL Database 服務在偵測到失敗且寬限期已過期之後，會觸發容錯移轉。 系統必須就影響級別，藉由 [SQL Database 服務的內建高可用性基礎結構](sql-database-high-availability.md)，來驗證中斷情況不會趨緩。 如果您想要控制應用程式的容錯移轉工作流程，可以關閉自動容錯移轉。
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **唯讀的容錯移轉原則**

  根據預設，唯讀接聽程式的容錯移轉已停用。 它可確保在次要伺服器離線時不會影響主要伺服器的性能。 不過，這也表示在次要伺服器恢復之前，唯讀的工作階段將無法連線。 If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **計劃性容錯移轉**

   計劃性容錯移轉會在主要資料庫和次要資料庫之間執行完整同步處理，然後將次要資料庫切換為主要角色。 這可確保不會遺失資料。 計劃性容錯移轉會用於下列案例：

  - 在資料遺失不可接受時，在生產環境中執行災害復原 (DR) 演練
  - 將資料庫重新放置到不同的區域
  - 在中斷情況趨緩 (容錯回復) 後，將資料庫傳回到主要區域。

- **非計劃性容錯移轉**

   非計劃性或強制容錯移轉會立即將次要資料庫切換為主要角色，而不會與主要資料庫進行任何同步處理。 這項作業會導致資料遺失。 非計劃性容錯移轉是主要資料庫無法存取時，用來作為中斷期間的復原方法。 When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **手動容錯移轉**

  無論自動容錯移轉設定為何，您都可以在任何時候手動起始容錯移轉。 如果未設定自動容錯移轉原則，就需要手動容錯移轉才能將容錯移轉群組中的資料庫復原至次要資料庫。 您可以起始強制或易用容錯移轉 (具有完整的資料同步處理)。 後者可用來將主要資料庫重新放置到次要區域。 容錯移轉完成時，DNS 記錄會自動更新以確保能連線到新的主要伺服器

- **資料遺失的寬限期**

  因為主要和次要資料庫是使用非同步複寫進行同步處理，容錯移轉可能會導致資料遺失。 您可以自訂自動容錯移轉原則，以反映應用程式對資料遺失的容錯程度。 By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **多個容錯移轉群組**

  您可以為相同的兩部伺服器設定多個容錯移轉群組來控制容錯移轉的規模。 每個群組分別進行容錯移轉。 如果您的多租用戶應用程式使用彈性集區，可以使用這項功能來混合每個集區中的主要和次要資料庫。 如此一來，可以讓中斷只影響一半的租用戶。

  > [!NOTE]
  > 受控執行個體不支援多個容錯移轉群組。
  
## <a name="permissions"></a>使用權限

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>將容錯移轉群組與單一資料庫和彈性集區一起使用的最佳做法

自動容錯移轉群組必須在主要 SQL Database 伺服器上設定，並將其連接至不同 Azure 區域中的次要 SQL Database 伺服器。 群組可以包含這些伺服器中的所有或部分資料庫。 下圖說明使用多個資料庫和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

在商務持續性前提下設計服務時，請遵循這些一般指導方針：

- **使用一或多個容錯移轉群組來管理多個資料庫的容錯移轉**

  可以在不同區域 (主要和次要伺服器) 的兩部伺服器之間建立一或多個容錯移轉群組。 每個群組可包含一或多個作為復原單位的資料庫，以防所有或部分的主要資料庫因為主要區域服務中斷而變成無法使用。 容錯移轉群組會使用和主要資料庫相同的服務目標，來建立異地次要資料庫。 如果您在容錯移轉群組中新增現有的異地複寫關聯性，請確定異地次要資料庫所設定的服務層級與計算大小和主要資料庫相同。
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **針對 OLTP 工作負載使用讀取寫入接聽程式**

  在執行 OLTP 作業時使用 `<fog-name>.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 請注意，容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。

- **針對唯讀工作負載使用唯讀接聽程式**

  如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 針對唯讀工作階段，請使用 `<fog-name>.secondary.database.windows.net` 作為伺服器 URL，連線會自動導向至次要伺服器。 It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **對效能降低做好心理準備**

  應用程式的其餘部分或所使用的其他服務並不會影響 SQL 的容錯移轉決策。 應用程式可能會「混用」某個區域的某些元件和另一個區域的某些元件。 若要避免效能降低，請務必要在 DR 區域中部署備援應用程式，並遵循[網路安全性指導方針](#failover-groups-and-network-security)。

  > [!NOTE]
  > DR 區域中的應用程式不一定非得使用不同的連接字串。  

- **對資料遺失做好心理準備**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. 預設值為 1 小時。 If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. 使用手動群組容錯移轉，從次要伺服器容錯回復到主要伺服器。

  > [!IMPORTANT]
  > 具有 800 個或更少 DTU 且使用異地複寫的資料庫超過 250 個的彈性集區，可能會遇到的問題包括規劃的容錯移轉時間較久與效能降低。  當地理複寫端點依地理位置廣泛相隔，或當每個資料庫使用多個次要端點時，較可能會發生這些寫入大量工作負載的問題。  異地複寫延遲隨時間而增加時，就可看出這些問題的徵兆。  您可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 來監視這個延遲。  如果發生這些問題，則風險降低方式包括增加集區 DTU 數目，或減少相同集區內異地複寫的資料庫數目。

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

自動容錯移轉群組必須在主要執行個體上設定，並將其連接至不同 Azure 區域中的次要執行個體。  執行個體中的所有資料庫都將複寫至次要執行個體。

下圖說明使用受控執行個體和自動容錯移轉群組之異地備援雲端應用程式的一般設定。

![自動容錯移轉](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **在與主要執行個體相同的 DNS 區域中建立次要執行個體**

  若要確保容錯移轉之後與主要執行個體的連線不中斷，主要和次要執行個體必須位於相同的 DNS 區域。 It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. 當您的應用程式準備好進行生產環境部署時，請在不同區域中建立次要執行個體，並確保它與主要執行個體共用 DNS 區域。 You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **在兩個執行個體之間啟用複寫流量**

  因為每個執行個體都在其自己的 VNet中隔離，因此必須允許這些 Vnet 之間的雙向流量。 請參閱 [Azure VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **設定容錯移轉群組來管理整個執行個體的容錯移轉**

  容錯移轉群組將會管理執行個體中所有資料庫的容錯移轉。 建立群組時，執行個體中的每個資料庫將會自動異地複寫到次要執行個體。 您無法使用容錯移轉群組來起始資料庫子集的部分容錯移轉。

  > [!IMPORTANT]
  > 如果從主要執行個體中移除資料庫，則它也將自動在異地次要執行個體上卸除。

- **針對 OLTP 工作負載使用讀取寫入接聽程式**

  在執行 OLTP 作業時使用 `<fog-name>.zone_id.database.windows.net` 作為伺服器 URL，連線會自動導向至主要伺服器。 在容錯移轉之後，不會變更此 URL。 容錯移轉牽涉到更新 DNS 記錄，因此只有在重新整理用戶端 DNS 快取之後，才會將用戶端連線重新導向至新的主要伺服器。 Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **直接連接到異地複寫的次要執行個體以進行唯讀查詢**

  如果您有容忍某些過時資料的邏輯隔離唯讀工作負載，則可以使用應用程式中的次要資料庫。 若要直接連接到異地複寫的次要執行個體，請使用 `server.secondary.zone_id.database.windows.net` 作為伺服器 URL，並直接連接到異地複寫的次要執行個體。

  > [!NOTE]
  > 在特定的服務層級，Azure SQL Database 支援使用[唯讀複本](sql-database-read-scale-out.md)來使用一個唯讀複本的容量，並使用連接字串中的 `ApplicationIntent=ReadOnly` 參數，對唯讀查詢工作負載進行負載平衡。 當您已設定異地複寫的次要執行個體時，可以使用此功能連接至主要位置或異地複寫位置中的唯讀複本。
  > - 若要連線至主要位置的唯讀複本，請使用 `<fog-name>.zone_id.database.windows.net`。
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **對效能降低做好心理準備**

  應用程式的其餘部分或所使用的其他服務並不會影響 SQL 的容錯移轉決策。 應用程式可能會「混用」某個區域的某些元件和另一個區域的某些元件。 若要避免效能降低，請務必要在 DR 區域中部署備援應用程式，並遵循[網路安全性指導方針](#failover-groups-and-network-security)。

- **對資料遺失做好心理準備**

  如果系統偵測到服務中斷，SQL 會在就我們所知並無資料遺失時，自動觸發讀寫容錯移轉。 否則，它會等候您透過 `GracePeriodWithDataLossHours` 所指定的這段時間。 如果您指定 `GracePeriodWithDataLossHours`，請針對資料遺失做好準備。 服務中斷期間，Azure 一般會傾向維持可用性。 如果您無法承擔資料遺失情況，請務必將 GracePeriodWithDataLossHours 設定為足夠大的數字，例如 24 小時。

  在起始容錯移轉之後，將立即發生讀寫接聽程式的 DNS 更新。 這項作業不會導致資料遺失。 但是，在正常情況下，切換資料庫角色的程序最多可能需要 5 分鐘的時間。 在完成之前，新的主要執行個體中的某些資料庫仍會處於唯讀模式。 If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. 如果使用 REST API 來起始，請使用標準 Azure Resource Manager 的輪詢機制來監視完成情況。

  > [!IMPORTANT]
  > 使用手動群組容錯移轉，將主要資料庫移回原始位置。 當造成容錯移轉的中斷情況趨緩時，您可以將主要資料庫移動到原始位置。 若要這樣做，您應該起始群組的手動容錯移轉。

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>容錯移轉群組和網路安全性

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用容錯移轉群組和虛擬網路規則

如果您使用[虛擬網路服務端點和規則](sql-database-vnet-service-endpoint-rule-overview.md)來限制存取您的 SQL 資料庫，請留意，每個虛擬網路服務端點只適用於一個 Azure 區域。 端點無法讓其他區域接受來自子網路的通訊。 因此，只有部署到相同區域中的用戶端應用程式可以連線到主要資料庫。 因為容錯移轉會導致 SQL 用戶端工作階段重新路由至不同 (次要) 區域中的伺服器，所以如果這些工作階段是來自該區域外的用戶端，就會失敗。 基於這個理由，如果參與的伺服器包含在虛擬網路規則中，則無法啟用自動容錯移轉原則。 若要支援手動容錯移轉，請遵循下列步驟：

1. 在次要地區中佈建您應用程式 (Web 服務、虛擬機器等) 前端元件的備援副本
2. 分別設定主要和次要伺服器的[虛擬網路規則](sql-database-vnet-service-endpoint-rule-overview.md)
3. 啟用[使用流量管理員設定的前端容錯移轉](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 偵測到中斷情況時，起始手動容錯移轉。 這個選項最適合用於在前端和資料層之間需要一致延遲的應用程式，且支援當前端、資料層或兩者受到中斷影響時進行復原。

> [!NOTE]
> 如果您使用**唯讀接聽程式**對唯讀工作負載進行負載平衡，請確保此工作負載會在 VM 或是次要地區的其他資源中執行，使其得以連線到次要資料庫。

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>使用容錯移轉群組和 SQL 資料庫防火牆規則

如果您的商務持續性計劃需要使用具有自動容錯移轉的群組，可以使用傳統的防火牆規則，來限制對您 SQL 資料庫的存取。 若要支援自動容錯移轉，請遵循下列步驟：

1. [建立公用 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [建立公用負載平衡器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer)並為其指派公用 IP。
3. [建立虛擬網路和虛擬機器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)以用於前端元件
4. [建立網路安全性群組](../virtual-network/security-overview.md)並設定輸入連線。
5. 請使用 'Sql' [服務標籤](../virtual-network/security-overview.md#service-tags)確定會開啟到 Azure SQL 資料庫的輸出連線。
6. 建立 [SQL 資料庫防火牆規則](sql-database-firewall-configure.md)以允許您在步驟 1 中所建立公用 IP 位址的輸入流量。

如需如何設定輸出存取，以及在防火牆規則中使用哪些 IP 的相關詳細資訊，請參閱[負載平衡器連出連線](../load-balancer/load-balancer-outbound-connections.md)。

上述組態可確保自動容錯移轉不會封鎖來自前端元件的連線，並假設應用程式可以容忍前端與資料層之間較長的延遲。

> [!IMPORTANT]
> 若要保證區域性中斷時的商務持續性，您必須確定前端元件與資料庫的異地備援。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. 目前不支援轉移的全域 VNet 對等互連。
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. 這是為了允許執行個體之間的複寫流量

   > [!IMPORTANT]
   > 設定錯誤的 NSG 安全性規則會導致資料庫複製作業停滯。

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>升級或降級主要資料庫

您可以將主要資料庫升級或降級至不同的計算大小 (在相同的服務層級內，而不是一般用途和業務關鍵之間)，而不需要將任何次要資料庫中斷連線。 When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. 當您將資料庫升級或降級到不同的服務層級時，會強制執行這項建議。

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> 如果您已在容錯移轉群組設定中建立次要資料庫，則不建議降級次要資料庫。 這是為了確保您的資料層在容錯移轉啟動之後有足夠的容量來處理一般工作負載。

## <a name="preventing-the-loss-of-critical-data"></a>防止重要資料遺失

由於廣域網路的高度延遲，連續複製採用非同步複寫機制。 如果發生失敗，非同步複寫導致部分資料遺失是無法避免的。 不過，有些應用程式可能會要求資料不能遺失。 若要保護這些重大更新，應用程式開發人員可以在認可交易後立即呼叫 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系統程序。 Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. 不過，它不會等候在次要資料庫上重新執行和認可傳輸的交易。 `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. 任何具備主要資料庫連接權限的使用者都可以呼叫此程序。

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>容錯移轉群組和時間點還原

如需使用容錯移轉群組中的時間點還原的相關資訊，請參閱[時間點復原 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="programmatically-managing-failover-groups"></a>以程式設計方式管理容錯移轉群組

如前所述，自動容錯移轉群組和主動式異地複寫也可以使用 Azure PowerShell 和 REST API，以程式設計的方式管理。 下表描述可用的命令集。 主動式異地複寫包含一組可管理的 Azure Resource Manager API，包括 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 這些 API 需要使用資源群組，並支援以角色為基礎的安全性 (RBAC)。 如需如何實作存取角色的詳細資訊，請參閱 [Azure 角色型存取控制](../role-based-access-control/overview.md)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用單一資料庫與彈性集區管理 SQL 資料庫容錯移轉

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 從伺服器移除容錯移轉群組，並刪除包含群組的所有次要資料庫 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 擷取容錯移轉群組設定 |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改容錯移轉群組的設定 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 觸發容錯移轉群組的容錯移轉到次要伺服器 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|將一或多個資料庫新增至 Azure SQL Database 容錯移轉群組|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |修改容錯移轉群組的設定|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |擷取容錯移轉群組設定|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |觸發容錯移轉群組的容錯移轉到次要伺服器|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 移除容錯移轉群組|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用單一資料庫與彈性集區管理 SQL 資料庫容錯移轉

| 命令 | 描述 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 從伺服器移除容錯移轉群組，並刪除包含群組的所有次要資料庫 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 擷取容錯移轉群組設定 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 觸發容錯移轉群組的容錯移轉到次要伺服器 |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| 命令 | 描述 |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 此命令會建立容錯移轉群組，並同時在主要和次要伺服器上註冊|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 修改容錯移轉群組的設定|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 擷取容錯移轉群組設定|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 觸發容錯移轉群組的容錯移轉到次要伺服器|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 移除容錯移轉群組 |

* * *

> [!IMPORTANT]
> 如需範例指令碼，請參閱[設定單一資料庫的容錯移轉群組並進行容錯移轉](scripts/sql-database-add-single-db-to-failover-group-powershell.md)。

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 從伺服器中移除容錯移轉群組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 從目前主要伺服器容錯移轉到此伺服器。 |
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |從目前主要伺服器容錯移轉到此伺服器。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 取得容錯移轉群組。 |
| [依伺服器列出容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出伺服器中的容錯移轉群組。 |
| [更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新容錯移轉群組。 |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API | 描述 |
| --- | --- |
| [建立或更新容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 建立或更新容錯移轉群組 |
| [刪除容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 從伺服器中移除容錯移轉群組 |
| [容錯移轉 (計劃性)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 從目前主要伺服器容錯移轉到此伺服器。 |
| [強制容錯移轉允許資料遺失](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |從目前主要伺服器容錯移轉到此伺服器。 這項作業可能會導致資料遺失。 |
| [取得容錯移轉群組](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 取得容錯移轉群組。 |
| [列出容錯移轉群組 - 依位置列出](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 列出位置中的容錯移轉群組。 |

## <a name="next-steps"></a>後續步驟

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- 如需範例指令碼，請參閱：
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 如需商務持續性概觀和案例，請參閱 [商務持續性概觀](sql-database-business-continuity.md)
- 若要了解 Azure SQL Database 自動備份，請參閱 [SQL Database 自動備份](sql-database-automated-backups.md)。
- 若要了解如何使用自動備份進行復原，請參閱 [從服務起始的備份還原資料庫](sql-database-recovery-using-backups.md)。
- 若要深入了解新的主要伺服器和資料庫的驗證需求，請參閱 [災害復原後的 SQL Database 安全性](sql-database-geo-replication-security-config.md)。
