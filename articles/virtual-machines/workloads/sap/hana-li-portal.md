---
title: 透過 Azure 入口網站的 Azure HANA 大型實例控制 |Microsoft Docs
description: 說明如何透過入口網站識別 Azure HANA 大型實例並與之互動的方式
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099823"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>透過 Azure 入口網站控制 Azure HANA Large Instances
本檔涵蓋[Hana 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)在[Azure 入口網站](https://portal.azure.com)中的呈現方式, 以及使用為您部署的 hana 大型實例單位 Azure 入口網站可以進行哪些活動。 Azure 入口網站中的 HANA 大型實例可見度是透過適用于 HANA 大型實例 (目前處於公開預覽狀態) 的 Azure 資源提供者提供。

## <a name="register-hana-large-instance-resource-provider"></a>註冊 HANA 大型實例資源提供者
通常, 您用於 HANA 大型實例部署的 Azure 訂用帳戶會針對 HANA 大型實例資源提供者註冊。 不過, 如果您看不到已部署的 HANA 大型實例單位, 您應該在您的 Azure 訂用帳戶中註冊資源提供者。 有兩種方式可以註冊 HANA 大型實例資源提供者

### <a name="register-through-cli-interface"></a>透過 CLI 介面註冊
您必須登入您的 Azure 訂用帳戶, 以便透過 Azure CLI 介面用於 HANA 大型實例部署。 您可以使用下列命令 (重新) 註冊 HANA 大型執行個體提供者:
    
    az provider register --namespace Microsoft.HanaOnAzure

如需詳細資訊, 請參閱[Azure 資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)一文


### <a name="register-through-azure-portal"></a>透過 Azure 入口網站進行註冊
您可以 (重新) 透過 Azure 入口網站註冊 HANA 大型實例資源提供者。 您需要在 Azure 入口網站中列出您的訂用帳戶, 然後按兩下用來部署 HANA 大型實例單位的訂用帳戶。 您在訂用帳戶的 [總覽] 頁面中, 選取 [資源提供者], 如下所示, 然後在 [搜尋] 視窗中輸入 "HANA"。 

![透過 Azure 入口網站註冊的](./media/hana-li-portal/portal-register-hli-rp.png)

在顯示的螢幕擷取畫面中, 已註冊資源提供者。 如果資源提供者尚未註冊, 請按 [重新註冊] 或 [註冊]。

如需詳細資訊, 請參閱[Azure 資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)一文


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>顯示 Azure 入口網站中的 HANA 大型實例單位
提交 HANA 大型實例部署要求時, 系統會要求您指定您要連接到 HANA 大型實例的 Azure 訂用帳戶。 建議使用相同的訂用帳戶, 來部署適用于 HANA 大型實例單位的 SAP 應用層。
當您的第一個 HANA 大型實例部署時, 系統會在您于 HANA 大型實例的部署要求中提交的 Azure 訂用帳戶中建立新的[azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。  新的資源群組將會列出您已在特定訂用帳戶中部署的所有 HANA 大型實例單位。

若要尋找新的 Azure 資源群組, 您可以藉由流覽 Azure 入口網站的左側流覽窗格, 列出訂用帳戶中的資源群組

![Azure 入口網站中的流覽窗格](./media/hana-li-portal/portal-resource-group.png)

在您要列出的資源群組清單中, 您可能需要篩選您用來部署 HANA 大型實例的訂用帳戶

![篩選 Azure 入口網站中的資源群組](./media/hana-li-portal/portal-filtering-subscription.png)

篩選到正確的訂用帳戶之後, 您仍然可以有一長串的資源群組。 尋找一個具有 **-Txxx**修正後的, 其中 "xxx" 為三位數, 例如 **-T050**。 

當您找到資源群組時, 請列出它的詳細資料。 您收到的清單可能如下所示:

![Azure 入口網站中的 B-HLI 清單](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有單位都代表已部署在您訂用帳戶中的單一 HANA 大型實例單位。 在此情況下, 您會查看您的訂用帳戶中已部署八個不同的 HANA 大型實例單位。

如果您已在相同的 Azure 訂用帳戶下部署數個 HANA 大型實例租使用者, 您會發現多個 Azure 資源群組 


## <a name="look-at-attributes-of-single-hli-unit"></a>查看第一部的的屬性
在 HANA 大型實例單位清單中, 您可以按一下單一單位, 然後取得單一 HANA 大型實例單位的詳細資料。 

在 [總覽] 畫面中, 按一下 [顯示更多] 之後, 您就會取得單元的呈現方式, 如下所示:

![顯示一間的概覽單位](./media/hana-li-portal/portal-show-overview.png)

查看顯示的不同屬性, 這些屬性看起來幾乎與 Azure VM 屬性截然不同。 在左側標頭中, 它會顯示資源群組、Azure 區域、訂用帳戶名稱和識別碼, 以及您新增的某些標記。 根據預設, HANA 大型實例單位不會指派任何標記。 在標頭右側, 當部署完成時, 單位的名稱會列為 [已指派]。 系統會顯示此作業系統, 以及 IP 位址。 就像 Vm 一樣, 也會顯示具有 CPU 執行緒和記憶體數目的 HANA 大型實例單位類型。 如需不同 HANA 大型實例單位的詳細資訊, 請參閱:

- [適用於 HLI 的可用 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP Hana (大型實例) 儲存體架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右下方的其他資料是 HANA 大型實例戳記的修訂。 可能的值包括：

- 修訂3
- 修訂4

修訂4是 HANA 大型實例發行的最新架構, 其主要改良了 Azure Vm 與在修訂版4戳記或資料列中部署的 HANA 大型實例單位之間的網路延遲。
您也可在總覽的右下角找到另一個非常重要的資訊, 以及針對每個已部署的 HANA 大型實例單位自動建立的 Azure 鄰近放置群組的名稱。 部署裝載 SAP 應用層的 Azure Vm 時, 必須參考此鄰近放置群組。 藉由使用與 HANA 大型實例單位相關聯的[azure 鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), 您可以確定 azure vm 已部署為與 Hana 大型實例單位密切接近的位置。 如何使用鄰近放置群組來尋找相同 Azure 資料中心內的 SAP 應用層, 以作為修訂版4裝載的 HANA 大型實例單位, 如需[sap 應用程式的最佳網路延遲, 請參閱 Azure 鄰近放置群組](sap-proximity-placement-scenarios.md).

標頭右側資料行中的其他欄位會通知 HANA 大型實例單位的電源狀態。

> [!NOTE]
> [電源狀態] 描述硬體單元是否開啟或關閉。 它不會提供正在啟動並執行之作業系統的相關資訊。 當您重新開機 HANA 大型實例單位時, 將會經歷一小段時間, 讓單元的狀態變更為 [**開始**], 進入 [**已啟動**] 的狀態。 處於 [**已啟動**] 狀態表示作業系統正在啟動, 或作業系統已完全啟動。 因此, 在重新開機單位之後, 一旦狀態切換為 [**已啟動**], 您就無法預期立即登入該單位。
> 

如果您按 [查看更多], 則會顯示其他資訊。 其中一個額外的資訊是顯示 HANA 大型實例戳記的修訂, 也就是已部署的單位。 如需不同的 [HANA 大型實例戳記修訂](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), 請參閱 <<c0>什麼是 Azure 上的 SAP Hana (大型實例)一文

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>檢查單一 HANA 大型實例單位的活動 
除了提供 HANA 大型實例單位的總覽外, 您還可以檢查特定單位的活動。 活動記錄檔看起來可能像這樣:

![Azure 入口網站中的流覽窗格](./media/hana-li-portal/portal-activity-list.png)

其中一個記錄的主要活動會重新開機單位。 列出的資料包括活動的狀態、活動所觸發的時間戳記、觸發活動的訂用帳戶識別碼, 以及觸發活動的 Azure 使用者。 

另一個正在進行記錄的活動會變更 Azure 中繼資料中的單位。 除了起始的重新開機之外, 您還可以看到**Write HANAInstances**的活動。 這種類型的活動不會對 HANA 大型實例單位本身執行任何變更, 而是會記錄 Azure 中單元的中繼資料變更。 在列出的案例中, 我們已新增並刪除標記 (請參閱下一節)。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>在 HANA 大型實例單位中新增和刪除 Azure 標記
另一個可能的原因是將[標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)新增至 HANA 大型實例單位。 標記被指派的方式與將標籤指派給 Vm 不同。 就像 Vm 一樣, 標記會存在於 Azure 中繼資料中, 而對於 HANA 大型實例, 其限制與 Vm 的標籤相同。

刪除標記的運作方式與 Vm 相同。 套用和刪除標記的兩個活動都會列在特定 HANA 大型實例單位的活動記錄中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>檢查 HANA 大型實例單位的屬性
區段**屬性**包含將實例遞交給您時所取得的重要資訊。 此區段可讓您取得在支援案例中可能需要的所有資訊, 或設定儲存體快照集設定時所需的資訊。 因此, 本節是您實例的資料集合、實例與 Azure 的連線, 以及儲存後端。 區段的頂端看起來像這樣:


![Azure 入口網站中的 [中] 屬性的上半部](./media/hana-li-portal/portal-properties-top.png)

前幾個資料項目, 您已經在 [總覽] 畫面中看到這些專案。 但是, 資料的重要部分是 ExpressRoute 線路識別碼, 您會在第一個部署的單元被遞交時得到。 在某些支援案例中, 您可能會收到該資料的要求。 重要的資料輸入會顯示在螢幕擷取畫面的底部。 顯示的資料是 NFS 儲存體標頭的 IP 位址, 可將您的儲存體隔離到 HANA 大型實例堆疊中的**租**使用者。 當您編輯[儲存體快照集備份的設定檔](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)時, 也需要此 IP 位址。 

當您在 [屬性] 窗格中向下滾動時, 會取得其他資料, 例如您的 HANA 大型實例單位的唯一資源識別碼, 或是指派給部署的訂用帳戶識別碼。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>透過 Azure 入口網站重新開機 HANA 大型實例單位
起始 Linux 作業系統的重新開機, 在各種情況下, OS 無法順利完成重新開機。 為了強制重新開機, 您需要開啟服務要求, 讓 Microsoft 作業執行 HANA 大型實例單位的電源重新開機。 HANA 大型實例單位的電源重新開機功能現在已整合到 Azure 入口網站中。 如同您在 HANA 大型實例單位的總覽部分, 您會在 [資料] 區段上看到 [重新開機] 按鈕

![Azure 入口網站中的重新開機步驟 #1](./media/hana-li-portal/portal-restart-first-step.png)

當您按下 [重新開機] 按鈕時, 系統會詢問您是否真的要重新開機單位。 當您按下 [是] 按鈕進行確認時, 該單位將會重新開機。

> [!NOTE]
> 在重新開機過程中, 您將會經歷一小段時間, 讓單元的狀態變更為 [**開始**], 進入 [**已啟動**] 的狀態。 處於 [**已啟動**] 狀態表示作業系統正在啟動, 或作業系統已完全啟動。 因此, 在重新開機單位之後, 一旦狀態切換為 [**已啟動**], 您就無法預期立即登入該單位。

> [!IMPORTANT]
> 視 HANA 大型實例單位中的記憶體數量而定, 重新開機和重新開機硬體和作業系統最多可能需要一小時的時間


## <a name="open-a-support-request-for-hana-large-instances"></a>開啟 HANA 大型實例的支援要求
在 Azure 入口網站顯示的 HANA 大型實例單位中, 您也可以針對 HANA 大型實例單位, 另外建立支援要求。 當您遵循連結**新的支援要求**時 

![在 Azure 入口網站中起始服務要求步驟 #1](./media/hana-li-portal/portal-initiate-support-request.png)

若要取得下一個畫面中所列 SAP Hana 大型實例的服務, 您可能需要選取 [所有服務], 如下所示

![選取 Azure 入口網站中的所有服務](./media/hana-li-portal/portal-create-service-request.png)

在服務清單中, 您可以找到服務**SAP Hana 大型實例**。 當您選擇該服務時, 您可以選取特定的問題類型, 如下所示:


![在 Azure 入口網站中選取問題類別](./media/hana-li-portal/portal-select-problem-class.png)

在每個不同的問題類型底下, 您都可以選擇所需的問題子類型, 以進一步描述問題的特性。 選取子型別之後, 您現在可以命名主旨。 完成選取程式之後, 您就可以移至建立的下一個步驟。 在 [**解決方案**] 區段中, 您會指向「HANA 大型實例」的相關檔, 這可能會提供問題解決方案的指標。 如果您在建議的檔中找不到問題的解決方案, 請移至下一個步驟。 在下一個步驟中, 系統會詢問您是否有使用 Vm 或使用 HANA 大型實例單位的問題。 此資訊可協助將支援要求導向至正確的專家。 

![Azure 入口網站中支援案例的詳細資料](./media/hana-li-portal/portal-support-request-details.png)

當您回答問題並提供其他詳細資料時, 您可以移至下一個步驟來審查支援要求並提交。

## <a name="next-steps"></a>後續步驟

- [如何監視 Azure 上的 SAP Hana (大型實例)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [從 HANA 端進行監視和疑難排解](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

