---
title: Azure HANA 大型執行個體控制透過 Azure 入口網站 |Microsoft Docs
description: 描述您可以識別，並透過入口網站與 Azure HANA 大型執行個體互動的方式
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/02/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8240308b3e0955b1d4d3ef2e82cad215daf95b00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482144"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>透過 Azure 入口網站控制 Azure HANA Large Instances
本文件涵蓋如何[HANA 大型執行個體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)按照[Azure 入口網站](https://portal.azure.com)和透過 Azure 入口網站會為您部署的 HANA 大型執行個體單位，可以執行哪些活動。 在 Azure 入口網站中 HANA 大型執行個體的可見性是透過 Azure 資源提供者提供的 HANA 大型執行個體，其中目前處於公開預覽狀態

## <a name="register-hana-large-instance-resource-provider"></a>註冊 HANA 大型執行個體的資源提供者
通常您使用 HANA 大型執行個體部署您 Azure 訂用帳戶註冊的 HANA 大型執行個體資源提供者。 不過，如果您無法看到您已部署的 HANA 大型執行個體單位，您就應該在您的 Azure 訂用帳戶註冊資源提供者。 有兩種方式註冊的 HANA 大型執行個體 」 資源提供者

### <a name="register-through-cli-interface"></a>透過 CLI 介面註冊
您必須登入您的 Azure 訂閱，用於 HANA 大型執行個體部署，透過 Azure CLI 介面。 您可以 （re） 註冊此命令使用 HANA 大型執行個體提供者：
    
    az provider register --namespace Microsoft.HanaOnAzure

如需詳細資訊，請參閱文章[Azure 資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)


### <a name="register-through-azure-portal"></a>透過 Azure 入口網站註冊
您可以 （re） 註冊 HANA 大型執行個體資源提供者透過 Azure 入口網站。 您要列出您在 Azure 入口網站中的訂用帳戶和訂用帳戶，用來部署您的 HANA 大型執行個體單位上按兩下。 其中一個您會在您的訂用帳戶中，[概觀] 頁面中選取 「 資源提供者 」，如下所示，[搜尋] 視窗中輸入 「 HANA"。 

![透過 Azure 入口網站註冊 HLI RP](./media/hana-li-portal/portal-register-hli-rp.png)

下列螢幕擷取畫面所示，資源提供者已經註冊。 如果尚未註冊資源提供者，請按 [re-register] 或 [註冊]。

如需詳細資訊，請參閱文章[Azure 資源提供者和類型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>在 Azure 入口網站中的 HANA 大型執行個體單位的顯示
提交的 HANA 大型執行個體部署要求，系統會要求您指定您要連接到 「 HANA 大型執行個體以及 Azure 訂用帳戶。 建議，使用相同的訂用帳戶用來部署適用於 HANA 大型執行個體單位的 SAP 應用程式層。
為您的第一個 HANA 大型執行個體部署，新[Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)會在您提交部署要求針對 HANA 大型執行個體中的 Azure 訂用帳戶中建立。  新的資源群組會列出所有您特定的訂用帳戶中，您已部署的 HANA 大型執行個體單位。

若要尋找新的 Azure 資源群組，您列出的資源群組中的訂用帳戶透過瀏覽 Azure 入口網站的左側的導覽窗格

![在 Azure 入口網站中瀏覽窗格](./media/hana-li-portal/portal-resource-group.png)

在資源群組清單中，列出您的開始，您可能需要以您用於已部署的 HANA 大型執行個體的訂用帳戶篩選

![篩選 Azure 入口網站中的資源群組](./media/hana-li-portal/portal-filtering-subscription.png)

篩選後的正確訂用帳戶，您仍可能有一長串的資源群組。 尋找有個 postfix **-Txxx** "xxx"為三位數，像是 **-T050**。 

如您找到的資源群組，列出它的詳細資料。 您收到的清單如下所示：

![在 Azure 入口網站中的 HLI 清單](./media/hana-li-portal/portal-hli-units-list.png)

列出的所有單元都代表已部署您的訂用帳戶中的單一 HANA 大型執行個體單位。 在此情況下，您看看八個不同 HANA 大型執行個體單位，您的訂用帳戶中部署。

如果您部署在相同的 Azure 訂用帳戶下的多個 HANA 大型執行個體租用戶，您會發現多個 Azure 資源群組 


## <a name="look-at-attributes-of-single-hli-unit"></a>看看單一 HLI 單位的屬性
在清單中的 HANA 大型執行個體單位，您可以按一下單一單位，並取得單一 HANA 大型執行個體單位的詳細資料。 

在 [概觀] 畫面中，您會收到的單位，如下所示的呈現方式：

![顯示 HLI 單位的概觀](./media/hana-li-portal/portal-show-overview.png)

看看顯示的不同屬性，這些屬性看起來不太不同於 Azure VM 屬性。 在左手邊的標頭，它會顯示資源群組、 Azure 區域、 訂用帳戶名稱和識別碼，以及您新增一些標記。 根據預設，HANA 大型執行個體單位會有任何指派的標記。 標頭的右手邊，部署已完成時所指派，會列出單位的名稱。 作業系統會顯示以及 IP 位址。 為 HANA 大型執行個體單位類型的 CPU 數目的 Vm 使用執行緒和記憶體也會顯示。 在不同的 HANA 大型執行個體單位上的更多詳細資料如下所示：

- [適用於 HLI 的可用 SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA （大型執行個體） 儲存體架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右側的資料行的標頭中的其他欄位會告知的 HANA 大型執行個體單位的電源狀態。

> [!NOTE]
> 電源狀態將告訴您是否要在開啟或關閉，採用硬體單位。 它並未正在啟動並執行的作業系統的相關資訊。 當您重新啟動 HANA 大型執行個體單位時，您將會遇到小時間單位的狀態會變為**起始**移動的狀態**已啟動**。 處於的狀態**已啟動**表示正在啟動作業系統，或作業系統，已完全啟動。 如此一來，單位的重新啟動之後, 您不能預期能夠立即登入的單位，狀態會切換成**已啟動**。
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>檢查單一 HANA 大型執行個體單位的活動 
超過提供的 HANA 大型執行個體單位的概觀，您可以檢查特定單元的活動。 活動記錄檔看起來像：

![在 Azure 入口網站中瀏覽窗格](./media/hana-li-portal/portal-activity-list.png)

其中一個主要的活動記錄會重新啟動的一個單位。 列出的資料包括活動，活動觸發，時間戳記的訂用帳戶識別碼的狀態已觸發活動和觸發活動的 Azure 使用者。 

正在記錄的另一個活動是 Azure 中繼資料中的單位的變更。 除了起始重新啟動，您可以看到活動的**寫入 HANAInstances**。 這種類型的活動本身的 HANA 大型執行個體單位上執行任何變更，但會記錄在 Azure 中之單位的中繼資料的變更。 中所列的情況下，我們要新增和刪除標記 （請參閱下一節）。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>新增和刪除到 HANA 大型執行個體單位的 Azure 標記
您擁有的另一個可能性是將[標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)至 HANA 大型執行個體單位。 取得指派標記的方式與將標記指派給 Vm，並無不同。 就像 Vm 標記存在於 Azure 的中繼資料，和 HANA 大型執行個體，必須為標籤適用於 Vm 相同的限制。

刪除標記 Vm 一樣運作方式相同。 這兩個活動，將套用和刪除標記會列出特定的 HANA 大型執行個體單位的活動記錄檔中。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>請檢查 HANA 大型執行個體單位的屬性
一節**屬性**包含執行個體傳送給您時的重要資訊。 它是您用來取得您可能需要支援的所有資訊的區段或設定儲存體快照集組態時，您需要的案例。 因此本節是您的執行個體的連線到 Azure 儲存體後端執行個體的相關資料的集合。 本節頂端看起來像：


![上半部的 Azure 入口網站中的 HLI 屬性](./media/hana-li-portal/portal-properties-top.png)

前幾個資料項目，您看到 [概觀] 畫面中已經。 但是，資料的重要部分是 ExpressRoute 線路識別碼，所得到的第一個部署的單位已移交時。 在某些支援情況下，可能會取得要求您提供該資料。 重要資料的項目會顯示在螢幕擷取畫面的底部。 顯示的資料是的 IP 位址的 NFS 儲存體標頭，隔離儲存體，以您**租用戶**HANA 大型執行個體堆疊中。 當您編輯時，也需要此 IP 位址[組態檔的儲存體快照集備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)。 

當您向下捲動 [屬性] 窗格中，您會取得額外資料，例如您的 HANA 大型執行個體單位的唯一資源識別碼或已指派給部署的訂用帳戶識別碼。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>透過 Azure 入口網站的 HANA 大型執行個體單位的重新啟動
起始重新啟動 Linux 作業系統時，有個不同的狀況下，OS 無法完成重新啟動成功。 若要強制重新啟動，您需要開啟服務要求，將執行的 HANA 大型執行個體單位的電源重新啟動的 Microsoft operations。 HANA 大型執行個體單位的電源重新啟動的功能現已整合到 Azure 入口網站。 由於您是在 HANA 大型執行個體單位的概觀部分，您會看到 資料 區段頂端的重新啟動 按鈕

![重新啟動 Azure 入口網站中的步驟 #1](./media/hana-li-portal/portal-restart-first-step.png)

如您所按下 [重新啟動] 按鈕，系統會要求您是否真的要重新啟動的單位。 您在確認"Yes"時按下按鈕，單位會重新啟動。

> [!NOTE]
> 重新啟動處理序中，將會經歷小時間單位的狀態會變為**起始**移動的狀態**Started**。 處於的狀態**已啟動**表示正在啟動作業系統，或作業系統，已完全啟動。 如此一來，單位的重新啟動之後, 您不能預期能夠立即登入的單位，狀態會切換成**已啟動**。

> [!IMPORTANT]
> 根據您的 HANA 大型執行個體單位中的記憶體數量，重新啟動和重新開機的硬體和作業系統可能需要最多一小時


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA 大型執行個體中開啟支援要求
從 Azure 入口網站顯示中的 HANA 大型執行個體單位，您可以建立支援要求，特別針對 HANA 大型執行個體單位也。 當您依照此連結**新增支援要求** 

![啟動 Azure 入口網站中的服務要求步驟 #1](./media/hana-li-portal/portal-initiate-support-request.png)

若要取得之服務的 SAP HANA 大型執行個體列在下一個畫面中，您可能需要選取 ' 所有服務 」，如下所示

![在 Azure 入口網站中選取所有的服務](./media/hana-li-portal/portal-create-service-request.png)

在服務清單中，您可以找到服務**SAP HANA 大型執行個體**。 當您選擇該服務時，您可以選取特定的問題類型，如所示：


![在 Azure 入口網站中選取問題類別](./media/hana-li-portal/portal-select-problem-class.png)

在每個不同的問題類型，您可以選取的問題子類型，您需要選取描述您在進一步的問題。 選取子類型之後, 現在您可以命名主體。 一旦您完成選取程序時，您可以移至下一個步驟建立。 在 [**解決方案**] 區段中，您是指 HANA 大型執行個體周圍的文件，這可能會讓指標的解決方案的問題。 如果您的問題，建議的文件中找不到方案，您會移至下一個步驟。 在下一個步驟中，您要的問題 Vm 還是與 HANA 大型執行個體單位的要求。 這項資訊可協助支援要求導向至正確的專家。 

![在 Azure 入口網站中的支援案例的詳細資料](./media/hana-li-portal/portal-support-request-details.png)

當您回答問題，並提供其他詳細資料，您可以移下一個步驟以複習支援要求並提交它。

## <a name="next-steps"></a>後續步驟

- [如何監視 Azure 上的 SAP HANA （大型執行個體）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [從 HANA 端進行監視和疑難排解](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

