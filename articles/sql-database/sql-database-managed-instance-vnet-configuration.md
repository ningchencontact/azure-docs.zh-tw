---
title: Azure SQL Database 受控執行個體 VNet 組態 | Microsoft Docs
description: 本主題描述具有 Azure SQL Database 受控執行個體之虛擬網路 (VNet) 的設定選項。
services: sql-database
author: srdjan-bozovic
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 45b645fe556d1f9630ee15b1f2f605bbaf3f87f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648059"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>設定 Azure SQL Database 受控執行個體的 VNet

Azure SQL Database 受控執行個體 (預覽) 必須部署在 Azure [虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 內。 此部署適用於下列案例： 
- 從內部部署網路直接連線到受控執行個體 
- 將受控執行個體連線到連結的伺服器或其他內部部署資料存放區 
- 將受控執行個體連線到 Azure 資源  

## <a name="plan"></a>規劃

使用您對於以下問題的答案，規劃要如何在虛擬網路中部署受控執行個體： 
- 您計劃部署單一或多個受控執行個體？ 

  受控執行個體的數目會決定要為受控執行個體配置的子網路大小下限。 如需詳細資訊，請參閱[決定受控執行個體的子網路大小](#create-a-new-virtual-network-for-managed-instances)。 
- 您是否需要將受控執行個體部署到現有虛擬網路，或者您要建立新的網路？ 

   如果您打算使用現有的虛擬網路，您需要修改該網路組態以順應您的受控執行個體。 如需詳細資訊，請參閱[針對受控執行個體修改現有的虛擬網路](#modify-an-existing-virtual-network-for-managed-instances)。 

   如果您打算建立新的虛擬網路，請參閱[針對受控執行個體建立新的虛擬網路](#create-a-new-virtual-network-for-managed-instances)。

## <a name="requirements"></a>需求

若要建立受控執行個體，您需要 VNet 內符合下列需求的專用子網路：
- **是空的**：子網路不能包含與其相關聯的任何其他雲端服務，且不能是閘道子網路。 您無法在包含受控執行個體以外資源的子網路中建立受控執行個體，或者稍後於子網路內新增其他資源。
- **沒有 NSG**：子網路不能有與其相關聯的網路安全性群組。
- **具有特定路由表**：子網路必須有使用者路由表 (UDR)，其中以 0.0.0.0/0 下一個躍點網際網路作為指派給它的唯一路由。 如需詳細資訊，請參閱[建立必要的路由表並產生關聯](#create-the-required-route-table-and-associate-it)
3. **選擇性自訂 DNS**：如果在 VNet 上指定自訂 DNS，則必須將 Azure 的遞迴解析程式 IP 位址 (例如 168.63.129.16) 新增至清單。 如需詳細資訊，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。
4. **沒有服務端點**：子網路不能有與其相關聯的服務端點 (儲存體或 SQL)。 請確定在建立 VNet 時停用 [服務端點] 選項。
5. **足夠的 IP 位址**：子網路必須至少具有 16 個 IP 位址。 如需詳細資訊，請參閱[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> 如果目的地子網路與上述所有需求不相容，您就無法部署新的受控執行個體。 目的地 Vnet 和子網路必須符合這些受控執行個體需求 (部署前和部署後)，任何違規都可能造成執行個體進入錯誤狀態而變得無法使用。 從該狀態復原時，您需要在 VNet 中以相容的網路原則建立新的執行個體、重新建立執行個體層級資料，以及還原您的資料庫。 這會對您的應用程式造成顯著的停機時間。

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>決定受控執行個體的子網路大小

當您建立受控執行個體時，Azure 會依據您在佈建期間選取的層大小來配置虛擬機器數目。 因為這些虛擬機器與您的子網路相關聯，所以它們需要 IP 位址。 為了確保正常作業和服務維護期間的高可用性，Azure 可能會配置額外的虛擬機器。 因此，子網路中所需的 IP 位址數目會大於該子網路中的受控執行個體數目。 

根據設計，受控執行個體在子網路中需要最少 16 個 IP 位址，可能會使用多達 256 個 IP 位址。 因此，您可以在定義子網路 IP 範圍時使用子網路遮罩 /28 到 /24。 

如果您打算在子網路內部署多個受控執行個體，且需要將子網路大小最佳化，請使用這些參數來進行計算： 

- Azure 會在子網路中針對自己的需求使用 5 個 IP 位址 
- 每個一般用途執行個體都需要 2 個位址 

**範例**：您打算有 8 個受控執行個體。 這表示您需要 5 + 8 * 2 = 21 個 IP 位址。 因為 IP 範圍是以 2 的乘冪定義，所以您需要 32 (2^5) 個 IP 位址的 IP 範圍。 因此，您需要保留子網路遮罩為 /27 的子網路。 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>為受控執行個體建立新的虛擬網路 

建立 Azure 虛擬網路是建立受控執行個體的必要條件。 您可以使用 Azure 入口網站、[PowerShell](../virtual-network/quick-create-powershell.md) 或 [Azure CLI](../virtual-network/quick-create-cli.md)。 下一節說明使用 Azure 入口網站的步驟。 這裡討論的詳細資料適用於每一種方法。

1. 按一下 Azure 入口網站左上角的 [建立資源]。
2. 尋找而後按一下 [虛擬網路]，確認已選取 [資源管理員] 作為部署模式，然後按一下 [建立]。

   ![虛擬網路建立](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 在虛擬網路表單中填妥所需的資訊，方式如以下螢幕擷取畫面所示：

   ![虛擬網路建立表單](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. 按一下頁面底部的 [新增] 。

   位址空間和子網路需以 CIDR 標記法指定。 

   > [!IMPORTANT]
   > 預設值會建立採用所有 VNet 位址空間的子網路。 如果選擇此選項，您就不能在虛擬網路內建立受控執行個體以外的任何其他資源。 

   建議的方法如下所示： 
   - 藉由遵循[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)一節來計算子網路大小  
   - 評估其餘 VNet 的需求 
   - 據以填入 VNet 和子網路位址範圍 

   請確定服務端點選項保持 [已停用]。 

   ![虛擬網路建立表單](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>建立必要的路由表並產生關聯

1. 登入 Azure 入口網站  
2. 尋找而後按一下 [路由表]，然後在 [路由表] 頁面上按一下 [建立]。

   ![路由表建立表單](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. 建立 0.0.0.0/0 下一個躍點網際網路路由，方式如以下螢幕擷取畫面所示：

   ![路由表新增](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![路由](./media/sql-database-managed-instance-tutorial/route.png)

4. 讓此路由與受控執行個體的子網路產生關聯，方式如以下螢幕擷取畫面所示：

    ![子網路](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![設定路由表](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![設定路由表 - 儲存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


一旦建立 VNet，您即可開始建立受控執行個體。  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>為受控執行個體修改現有的虛擬網路 

本節中的問題和答案說明如何將受控執行個體新增至現有的虛擬網路。 

**您要對現有的虛擬網路使用傳統或 Resource Manager 部署模型？** 

您只能在 Resource Manager 虛擬網路中建立受控執行個體。 

**您要為 SQL 受控執行個體建立新的子網路或使用現有的子網路？**

如果您想要建立新的子網路： 

- 藉由遵循[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)一節中的指導方針來計算子網路大小。
- 遵循[新增、變更或刪除虛擬網路子網路](../virtual-network/virtual-network-manage-subnet.md)中的步驟。 
- 建立路由表，其中包含單一項目 **0.0.0.0/0** 作為下一個躍點網際網路，並且讓它與受控執行個體的子網路相關聯。  

如果您想要在現有的子網路內建立受控執行個體： 
- 檢查子網路是否是空的 - 無法在包含其他資源 (包括閘道子網路) 的子網路中建立受控執行個體 
- 藉由遵循[決定受控執行個體的子網路大小](#determine-the-size-of-subnet-for-managed-instances)一節中的指導方針來計算子網路大小，並且確認其大小適當。 
- 確認子網路上的服務端點未啟用。
- 確認沒有任何網路安全性群組與子網路相關聯 

**您是否已設定自訂 DNS 伺服器？** 

如果是的話，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)。 

- 建立必要的路由表並產生關聯：請參閱[建立必要的路由表並產生關聯](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何建立 VNet、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-create-tutorial-portal.md)。
- 針對 DNS 問題，請參閱[設定自訂 DNS](sql-database-managed-instance-custom-dns.md)
