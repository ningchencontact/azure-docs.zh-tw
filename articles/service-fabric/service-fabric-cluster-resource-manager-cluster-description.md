---
title: 描述叢集使用叢集 Resource Manager |Microsoft Docs
description: 指定容錯網域、 升級網域、 節點屬性和節點容量的叢集資源管理員，以描述 Service Fabric 叢集。
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271639"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>描述 Service Fabric 叢集使用叢集資源管理員
Azure Service Fabric 的叢集資源管理員功能提供數種機制，來描述叢集：

* 容錯網域
* 升級網域
* 節點屬性
* 節點容量

在執行階段，叢集資源管理員會使用這項資訊，以確保叢集中執行之服務的高可用性。 在強制執行這些重要規則，它也會嘗試在叢集內的資源使用最佳化。

## <a name="fault-domains"></a>容錯網域
容錯網域是協調失敗的任何區域。 在單一機器是一個容錯網域。 在 NIC 韌體不正確的磁碟機失敗自己基於各種原因，從電源供應器故障，它可能會失敗。 

連接到相同的乙太網路交換器的機器都位於相同的容錯網域。 因此會共用單一來源的能力，或在單一位置中的機器。 

由於這是重疊的硬體故障天生，容錯網域是原本就是階層式。 它們是在 Service Fabric 中以 Uri 表示。

很重要，容錯網域已正確設定因為 Service Fabric 會使用這項資訊來安全地放置服務。 Service Fabric 不想要放置服務時會注意因為遺失容錯網域 （某個元件失敗所造成） 而導致服務中斷。 

在 Azure 環境中，Service Fabric 會正確地設定代表您叢集中的節點，使用環境所提供的容錯網域資訊。 Service fabric 的獨立執行個體，容錯網域會定義在設定叢集的時間。 

> [!WARNING]
> 請務必提供給 Service Fabric 的容錯網域資訊正確無誤。 例如，假設 Service Fabric 叢集的節點正在執行，5 的實體主機上執行的 10 個虛擬機器內。 在此情況下，即使有 10 部虛擬機器，也只有 5 個不同的 (最上層) 容錯網域。 共用相同的實體主機會造成 Vm 共用相同的根容錯網域，因為如果其實體主機失敗，Vm 會遇到協調的失敗。  
>
> Service Fabric 預期節點未變更的容錯網域。 其他機制，確保高可用性的 Vm，例如[Ha-vm](https://technet.microsoft.com/library/cc967323.aspx)，使用 Service Fabric 可能會導致衝突。 這些機制會使用從另一部主機的 Vm 透明移轉。 它們不重新設定，或通知在 VM 內執行的程式碼。 因此，它們*不支援*為環境執行 Service Fabric 叢集。 
>
> Service Fabric 應該僅採用高可用性技術。 不需要像是即時 VM 移轉和 San 的機制。 如果使用 Service Fabric 搭配使用這些機制他們_減少_應用程式可用性和可靠性。 原因是它們導致額外的複雜性、 新增集中式失敗，來源和使用 Service Fabric 中的衝突的可靠性和可用性策略。 
>
>

在下圖中，我們色彩參與容錯網域和列出產生的所有不同容錯網域的所有實體。 在此範例中，我們有資料中心 ("DC")、機架 ("R") 和刀鋒伺服器 ("B")。 如果每個刀鋒視窗包含一個以上的虛擬機器，容錯網域階層中可能有另一層。

<center>

![透過容錯網域組織的節點][Image1]
</center>

在執行階段，Service Fabric 叢集資源管理員會考慮叢集中的容錯網域，並規劃配置。 具狀態複本或無狀態服務的執行個體，會讓它們在不同的容錯網域散發。 跨容錯網域分散服務，可確保在階層的任何層級的容錯網域失敗時，不危害服務的可用性。

叢集資源管理員不在乎容錯網域階層中有多少層級。 它會嘗試確保遺失階層的任何一部分並不會影響在其中執行的服務。 

如果節點數目相同容錯網域階層中的每個層級，它是深度的最佳的。 如果容錯網域 「 樹狀結構 」 在叢集中不對稱的很難的叢集資源管理員找出服務的最佳配置。 不平衡的容錯網域配置表示遺失某些網域會影響服務大於其他網域的可用性。 如此一來，叢集資源管理員是兩個目標之間掙扎： 

* 它想要使用該 「 重負荷 」 網域中的機器，藉由將服務放在其上。 
* 它想要將服務放在其他網域，因為遺失網域而造成的問題。 

不平衡網域外觀為何？ 下圖顯示兩個不同的叢集配置。 在第一個範例中，節點平均分散至容錯網域。 在第二個範例中，一個容錯網域會有更多的節點比其他容錯網域。 

<center>

![兩個不同的叢集配置][Image2]
</center>

在 Azure 中，的選擇哪一個容錯網域包含節點會替您。 但是，根據您佈建的節點數目，您仍然得到在其他方式比有更多節點的容錯網域。 

例如，假設您有五個容錯網域，在叢集中，但佈建節點類型的七個節點 (**NodeType**)。 在此情況下，第一次的兩個容錯網域最後會有多個節點。 如果您繼續部署更多**NodeType**執行個體與只使用幾個情況下，問題會更糟。 基於這個理由，建議您在每個節點類型中的節點數目是容錯網域數目的倍數。

## <a name="upgrade-domains"></a>升級網域
升級網域是另一項功能，可協助 Service Fabric 叢集資源管理員了解叢集的配置。 升級網域會定義在相同的時間升級的節點集。 升級網域可協助叢集資源管理員了解及協調例如升級的管理作業。

升級網域非常類似容錯網域，但有幾個主要的差異。 首先，協調的硬體失敗的區域定義容錯網域。 相反地，升級網域，會定義原則。 您可以決定想要多少個，而不是讓指定數目的環境。 您可以有多個升級網域節點。 容錯網域和升級網域的另一個差異在於升級網域不是階層式。 相反地，它們更像是簡單的標記。 

下圖顯示三個容錯網域等量的三個升級網域。 它也會示範一個可能的位置的三個不同複本的具狀態服務，其中每一個最後都在不同錯誤網域和升級網域。 這個位置可讓服務升級期間，即使容錯網域的遺失，且仍有一份程式碼和資料。  

<center>

![放置與容錯網域和升級網域][Image3]
</center>

有的利與弊擁有大量升級網域。 升級網域表示升級的每個步驟更細微，且會影響較少的節點或服務。 較少的服務必須一次移動到系統的影響較小。 這樣較容易改善可靠性，因為較少的服務會受到升級期間的任何問題。 升級網域也表示您需要較少的可用緩衝區來處理升級的影響的其他節點上。 

例如，如果您有五個升級網域，在每個節點可處理大約 20%的流量。 如果您需要關閉升級網域以進行升級，則負載通常必須移至別處。 由於您有四個剩餘的升級網域，每個必須有大約總流量 5%的空間。 升級網域表示您需要較少的緩衝區上的叢集中的節點。 

請考慮是否您改為有 10 個升級網域。 在此情況下，每個升級網域也會處理僅大約總流量的 10%。 當升級步驟是透過叢集時，每個網域必須有大約只總流量 1.1%的空間。 升級網域通常可讓您執行您的節點，在更高的使用率，因為您需要較少的保留容量。 這也適用於容錯網域。  

擁有許多升級網域的缺點是升級通常需要較長時間。 Service Fabric 會等候升級網域完成後會執行檢查，再開始升級下一個在短時間。 這些延遲可讓系統在升級繼續之前偵測由升級帶來的問題。 缺點是可接受的，因為它會防止不良的變更一次影響到太多服務。

升級網域太少有與否有許多負面的副作用。 每個升級網域時向下，正在升級，大部分的整體容量無法使用。 例如，如果您有只有三個升級網域，你會關閉大約 1/3 的整體服務或叢集容量一次。 有許多服務的向下一次不理想，因為您需要在叢集的其餘部分足夠容量來處理工作負載。 維護該緩衝區所表示的是，在正常作業期間，這些節點就會是小於負載比其他。 這會增加執行服務的成本。

環境中容錯或升級網域的總數沒有實際限制，對於它們如何重疊也沒有條件約束。 但是有常見的模式：

- 容錯網域和升級網域 1:1 對應
- 每個節點 （實體或虛擬作業系統執行個體） 一個升級網域
- 「 等量 」 或 「 矩陣 」 模型，其中的容錯網域和升級網域形成一個矩陣，而電腦通常沿著對角線排列。

<center>

![容錯和升級網域的版面配置][Image4]
</center>

沒有選擇哪個配置找到最佳解答。 各有其優缺點。 例如，1FD:1UD 模型相當容易設定。 每個節點模型的一個升級網域的模型是最例如哪些人來。 在升級期間，每個節點會獨立更新。 這類似於以往手動升級小型集合機器的方式。

最常見的模型是 FD/UD 矩陣，其中的容錯網域和升級網域形成一個表格，而節點沿著對角線開始放置。 這是在 Azure 中的 Service Fabric 叢集預設使用的模型。 對於具有許多節點的叢集，所有項目最後看起來就像是密集矩陣模式。

> [!NOTE]
> 在 Azure 中裝載的 Service Fabric 叢集不支援變更預設策略。 只有獨立叢集可提供該自訂。
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>容錯和升級網域條件約束及產生的行為
### <a name="default-approach"></a>預設方法
根據預設，叢集資源管理員會保留平衡跨容錯網域和升級網域的服務。 這會經由模型化成為[條件約束](service-fabric-cluster-resource-manager-management-integration.md)。 容錯和升級網域狀態的條件約束：「 針對特定的服務分割區中，應該永遠不會有差異大於一的相同階層層級上任兩個網域之間 （無狀態服務執行個體或具狀態服務複本） 的服務物件數目 」。

例如，假設此條件約束提供 「 差異上限 」 保證。 容錯和升級網域條件約束可防止某些措施或安排違反此規則。

例如，假設我們有六個節點，設定五個容錯網域和五個升級網域的叢集。

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

現在假設我們建立的服務**TargetReplicaSetSize** (或無狀態服務，如**InstanceCount**) 的五個值。 複本將會落在 N1-N5 上。 事實上，不論建立多少像這樣的服務，都不會用到 N6。 原因為何？ 讓我們看看目前的配置和選擇 N6 時情況如何之間的差異。

以下是我們的配置和每個容錯和升級網域的複本總數：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置達到平衡每個容錯網域和升級網域的節點數。 它是每個容錯和升級網域的複本數目方面也達到平衡。 每個網域都擁有相同數量的節點，以及相同數量的複本。

現在，讓我們看看改用 N6 而不使用 N2 的情況。 複本將會如何散佈？

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

此配置違反容錯網域條件約束的 「 差異上限 」 保證的定義。 FD0 有兩個複本，而 FD1 沒有。 FD0 與 FD1 之間的差異是合計為 2，大於差異上限 1。 因為違反條件約束時，叢集資源管理員不允許這種排列方式。 同樣地，如果挑選 N2 和 N6 （而不是 N1 和 N2），我們會得到：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

此配置就容錯網域而言被平衡的。 但現在它會違反升級網域條件約束，因為 ud0 沒有複本，而 UD1 有兩個。 此配置也無效，而且不會選取由叢集資源管理員。

這個分配具狀態複本或無狀態執行個體的方法提供了可能的最佳容錯效果。 如果有一個網域停止運作，最小數量的複本/執行個體將會遺失。 

另一方面，此方法則可能太過嚴苛，而不允許叢集利用所有資源。 就某些叢集設定而言，會無法使用某些節點。 這會導致無法將您的服務，產生警告訊息中的 Service Fabric。 在上述範例中，某些叢集節點不能使用 (此範例中的 N6)。 即使您將節點加入該叢集 (N7 N10) 時，就只能在 N1 – N5 上放複本/執行個體，因為容錯和升級網域條件約束。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>替代方法

叢集資源管理員支援容錯和升級網域條件約束的另一個版本。 它允許安置，同時也保證最低限度的安全性。 替代的條件約束可陳述如下：「 針對特定的服務資料分割複本在網域的分配應該確保資料分割不會發生仲裁遺失。 」 例如，假設此條件約束提供 「 仲裁防護 」 保證。 

> [!NOTE]
> 就具狀態服務而言，我們將「仲裁遺失」  定義為一種大多數分割區複本都同時停止運作的情況。 例如，如果**TargetReplicaSetSize**為 5，任何三個複本的一組代表仲裁。 同樣地，如果**TargetReplicaSetSize**為六，四個複本所需的仲裁。 在這兩種情況下，不能超過兩個複本可中斷在同一時間如果分割區想要繼續正常運作。 
>
> 無狀態服務，沒有沒有所謂*仲裁遺失*。 無狀態服務繼續正常運作，即使大多數執行個體在相同的時間停機。 因此，我們將著重在本文的其餘部分中的具狀態服務。
>

讓我們回到先前的範例。 使用 「 仲裁防護 」 版本條件約束時，所有的三個配置會有效。 即使無法在第二個配置中的 FD0 或第三個配置中的 UD1 失敗，分割區仍然會有仲裁。 （大多數複本仍然會正常運作。）使用條件約束的這個版本，可以幾乎一律會使用 N6。

「 仲裁防護 」 方法提供更大的彈性比 「 差異上限 」 方法。 原因是您更輕鬆地找到在幾乎任何叢集拓撲中都有效的複本散發套件。 不過，此方法無法保證最佳容錯特性，因為有些錯誤比其他錯誤來得嚴重。 

在最糟的情況下，大部分的複本可以中斷而失敗的一個網域和一個額外的複本。 比方說，而不是正在失去仲裁，具有五個複本或執行個體所需的三個失敗，您現在可以遺失大多數只有兩個失敗。 

### <a name="adaptive-approach"></a>調適型方法
由於這兩種方法有優點和缺點，我們引入了結合這些兩種策略的調適型方法。

> [!NOTE]
> 這是從 Service Fabric 6.2 版開始的預設行為。 
> 
> 調適型方法預設會使用「差異上限」邏輯，而只有在必要時，才會切換至「仲裁防護」邏輯。 叢集資源管理員會自動查明哪個策略是必要藉由查看叢集和服務的設定方式。
> 
> 叢集資源管理員應該使用 「 仲裁型 」 邏輯，則為 true 的服務這兩個條件為：
>
> * **TargetReplicaSetSize**服務是可由數個容錯網域和升級網域數目。
> * 節點數目小於或等於數字乘以升級網域數目的容錯網域。
>
> 記住，叢集資源管理員會使用這種方法對於無狀態與具狀態服務，即使針對無狀態服務的仲裁遺失狀態無關。

讓我們回到先前的範例，假設叢集現在有八個節點。 叢集在 client1 仍然具有五個容錯網域和五個升級網域，而**TargetReplicaSetSize**該叢集上裝載之服務的值仍然維持五個。 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

符合所有必要的條件，因為叢集資源管理員會使用 「 仲裁型 」 邏輯來分配服務。 這會允許使用 N6 N8。 一個可能的服務分配在此情況下可能會看起來像這樣：

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

如果您的服務**TargetReplicaSetSize** （舉例來說） 值縮減為 4，叢集資源管理員會注意到該變更。 它會繼續使用 「 差異上限 」 邏輯，因為**TargetReplicaSetSize**無法再又的容錯網域和升級網域數目。 如此一來，某些複本移動會分散在節點 N1-N5 上剩餘的四個複本。 如此一來，不違反容錯網域和升級網域邏輯的 「 差異上限 」 版本。 

在先前的配置中，如果**TargetReplicaSetSize**值為 5 和從叢集中移除 N1，升級網域數目會變成等於 4。 同樣地，叢集資源管理員會開始使用 「 差異上限 」 邏輯，因為升級網域數目整除服務的**TargetReplicaSetSize**不再值。 如此一來，複本 R1，同樣地，建置時必須放在 N4 上，以便不會違反容錯和升級網域條件約束。

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>設定容錯和升級網域
在 Azure 託管的 Service Fabric 部署中，會自動定義容錯網域和升級網域。 Service Fabric 會從 Azure 中取用環境資訊。

如果您要建立您自己的叢集 （或想要在開發過程中執行特定拓撲），您可以提供容錯網域，並自行升級網域資訊。 在此範例中，我們會定義九個節點的本機開發叢集跨越三個資料中心 （各有三個機架）。 此叢集也有等量分散於這些三個資料中心的三個升級網域。 ClusterManifest.xml 中組態的範例如下：

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

此範例會使用 ClusterConfig.json 獨立部署：

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> 當您定義叢集透過 Azure Resource Manager 時，Azure 將容錯網域和升級網域。 定義您的節點類型與虛擬機器擴展集中，因此您的 Azure Resource Manager 範本不包含容錯網域或升級網域的相關資訊。
>

## <a name="node-properties-and-placement-constraints"></a>節點屬性和放置條件約束
有時候 （事實上，大部分的情況），您會想要確保只在特定類型的叢集中的節點上執行某些工作負載。 例如，某些工作負載可能需要 Gpu 或 Ssd，和其他人不可能。 

將硬體專用於特定工作負載的絕佳範例是幾乎所有的多層式架構。 特定電腦做為前端或 API 伺服端的應用程式，並公開至用戶端或網際網路。 其他電腦 (通常有不同的硬體資源) 處理計算層或儲存層的工作。 它們通常_不會_直接公開至用戶端或網際網路。 

Service Fabric 預期，在某些情況下，特定的工作負載可能需要在特定硬體組態上執行。 例如:

* 在 Service Fabric 環境已 「 提升並移轉 」 現有的多層式架構應用程式。
* 工作負載必須在效能、 級別或安全性隔離原因的特定硬體上執行。
* 工作負載應該彼此隔離的原則或資源耗用量的理由。

若要支援這種組態，Service Fabric 會包含您可以套用至節點的標記。 這些標記稱為*節點屬性*。 *放置條件約束*會附加至您選取一或多個節點屬性的個別服務的陳述式。 放置條件約束會定義應該執行服務的位置。 條件約束的集合是可延伸的。 任何索引鍵/值組都沒問題。 

<center>

![叢集配置不同的工作負載][Image5]
</center>

### <a name="built-in-node-properties"></a>內建的節點屬性
Service Fabric 定義一些預設節點屬性，因此您不需要加以定義可以自動使用。 每個節點所定義的預設屬性是**NodeType**並**NodeName**。 

比方說，您可以撰寫為放置條件約束`"(NodeType == NodeType03)"`。 **NodeType**是常用的屬性。 這是很有用，因為 1 對 1 對應類型的機器。 每個機器類型都對應至傳統多層式架構應用程式中的工作負載類型。

<center>

![放置條件約束和節點屬性][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>放置條件約束和節點屬性語法 
節點屬性中指定的值可以是字串、 布林值，或帶正負號長時間。 在服務的陳述式則稱為放置*條件約束*因為它會限制服務可以執行在叢集中的位置。 條件約束可以是任何在叢集中的節點屬性運作的布林值陳述式。 這些布林值的陳述式中的有效選取器如下：

* 條件會檢查建立特定陳述式：

  | 陳述式 | 語法 |
  | --- |:---:|
  | 「等於」 | "==" |
  | 「不等於」 | "!=" |
  | 「大於」 | ">" |
  | 「大於或等於」 | ">=" |
  | 「小於」 | "<" |
  | 「小於或等於」 | "<=" |

* 分組和邏輯作業的布林值陳述式：

  | 陳述式 | 語法 |
  | --- |:---:|
  | 「和」 | "&&" |
  | 「或」 | "&#124;&#124;" |
  | 「否」 | "!" |
  | 「組成單一陳述式」 | "()" |

以下是一些基本的條件約束陳述式的範例：

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

服務只能放置在整體放置條件約束陳述式評估為 "True" 的節點上。 沒有定義屬性的節點不符合包含該屬性的任何放置條件約束。

例如，假設下列節點屬性所定義的 ClusterManifest.xml 中的節點類型：

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

下列範例會示範透過 ClusterConfig.json 獨立部署或 Template.json 定義 Azure 託管叢集的節點屬性。 

> [!NOTE]
> 在 Azure Resource Manager 範本中，節點類型通常已參數化。 所以看起來像`"[parameters('vmNodeType1Name')]"`而不是 NodeType01。
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

您可以建立服務放置*條件約束*服務，如下所示：

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

如果 NodeType01 的所有節點都都有效，您也可以選取該節點類型，與條件約束`"(NodeType == NodeType01)"`。

可以在執行階段期間動態更新服務的放置條件約束。 如果您需要您可以在叢集中移動服務、 新增和移除需求，等等。 Service Fabric 可確保服務保持執行且可用即使進行這類變更。

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

每個具名的服務執行個體指定放置條件約束。 更新一律會取代 (覆寫) 先前指定的項目。

叢集定義會定義節點上的屬性。 變更節點的屬性需要叢集設定升級。 升級節點的屬性需要每個受影響的節點重新啟動，以報告其新的屬性。 Service Fabric 會管理這些輪流升級。

## <a name="describing-and-managing-cluster-resources"></a>描述與管理叢集資源
任何 Orchestrator 的其中一個最重要的作業是協助管理叢集中的資源耗用量。 管理叢集資源可以表示幾個不同的項目。 

首先，確保電腦不會多載。 這表示確定電腦不會執行比它們能夠處理還多的服務。 

其次，會有平衡和最佳化，是很重要，有效率地執行服務。 符合成本效益或效能敏感服務供應項目不允許某些是熱的而其他是冷的節點。 忙碌節點會導致資源爭用和效能不佳。 閒置節點代表浪費的資源和增加的成本。 

Service Fabric 代表做為資源*計量*。 度量是您想要向 Service Fabric 描述的任何邏輯或實體資源。 度量的範例是"WorkQueueDepth"或"MemoryInMb"。 在節點上的 Service Fabric 可管理的實體資源的相關資訊，請參閱[資源控管](service-fabric-resource-governance.md)。 如需設定自訂的度量及其用法的資訊，請參閱[這篇文章](service-fabric-cluster-resource-manager-metrics.md)。

計量是不同於放置條件約束和節點屬性。 節點屬性是節點本身的靜態描述項。 計量說明節點所擁有的資源，並在節點上執行時，服務會使用。 節點屬性可能**HasSSD** ，而且可能會設定為 true 或 false。 該 SSD 和多少會取用服務上的可用空間數量是像"drivespaceinmb"之類。 的計量 

就像放置條件約束和節點屬性，Service Fabric 叢集 Resource Manager 並不了解計量平均值的名稱。 計量名稱只是字串。 您最好宣告單位時可能會模稜兩可，您所建立的計量名稱的一部分。

## <a name="capacity"></a>容量
如果您關閉所有的資源*平衡*，Service Fabric 叢集資源管理員仍會確保節點不會超過其容量。 除非叢集已滿或工作負載大於任何節點，否則管理容量溢位是可能的。 容量是另一個*條件約束*，叢集資源管理員會使用可了解節點的資源量。 另外也會追蹤整個叢集的剩餘容量。 

服務層級的容量和耗用量均以度量來表示。 例如，計量可能是"ClientConnections"，而節點可能會是"ClientConnections"32,768 的容量。 其他節點可以有其他限制。 在該節點上執行的服務可以說它目前耗用 32,256 的計量"ClientConnections"。

在執行階段，叢集資源管理員會追蹤叢集中和節點上的剩餘容量。 若要追蹤容量，叢集資源管理員會減去每個服務的使用方式，從執行服務所在的節點容量。 利用此資訊，叢集資源管理員可找出要放置或移動複本，使節點不超出容量。

<center>

![叢集節點和容量][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

您可以看到叢集資訊清單中定義的容量。 以下是 ClusterManifest.xml 的範例：

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

針對獨立部署或 Template.json 定義透過 ClusterConfig.json Azure 託管叢集容量的範例如下： 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

服務的動態負載經常會變更。 說到複本的負載"Clientconnections"從 1024 中變更為 2,048。 它執行所在節點的容量為只剩下該計量的 512。 現在該複本或執行個體的位置無效，因為該節點上沒有足夠的空間。 叢集資源管理員必須讓節點回到容量以下。 它可減少超過容量是由來自該節點的一或多個複本或執行個體移到其他節點的節點上的負載。 

叢集資源管理員會嘗試移動複本的成本降到最低。 您可以深入了解[移動成本](service-fabric-cluster-resource-manager-movement-cost.md)和 關於[重新平衡策略和規則](service-fabric-cluster-resource-manager-metrics.md)。

## <a name="cluster-capacity"></a>叢集容量
Service Fabric 叢集資源管理員如何讓整體叢集太滿？ 動態負載下，沒有很多，它可以執行。 服務可以有獨立叢集資源管理員採取的動作，其負載可能突然增加。 如此一來，您有充足的空餘空間目前的叢集可能太差，如果沒有突然增加的未來。 

在 「 叢集資源管理員 」 中的控制項協助避免發生問題。 您可以執行第一件事是防止建立新的工作負載，會導致叢集空間變滿。

假設您建立無狀態服務，而它有一些與它相關聯的負載。 服務很注重"DiskSpaceInMb"計量。 服務會耗用五個單位的"DiskSpaceInMb"，針對每個服務執行個體。 您想要建立服務的三個執行個體。 這表示您需要 15 個單位的"DiskSpaceInMb"會出現在您建立這些服務執行個體的叢集。

叢集資源管理員持續計算容量和每個度量的耗用量，因此可以判斷叢集中的剩餘容量。 如果沒有足夠的空間，叢集資源管理員會拒絕建立服務的呼叫。

由於需求僅是 15 個單位會提供，您可以在許多不同的方式配置此空間。 比方說，有可能剩餘一個單位的容量 15 個不同的節點或上五個不同的節點容量的剩餘的三個單位。 如果叢集資源管理員能夠重新安排讓三個節點上有五個單位，它會放置此服務。 重新安排叢集通常都可行，除非叢集幾乎已滿，或因為某些原因致使現有服務無法合併。

## <a name="buffered-capacity"></a>緩衝處理的容量
緩衝的容量是另一項功能的叢集資源管理員。 它可以保留整體節點容量的某些部分。 這個容量緩衝區只能用於在升級和節點失敗期間放置服務。 

每個計量的所有節點全域指定緩衝處理的容量。 您挑選的保留容量的值是您在叢集中的容錯和升級網域數目的函式。 容錯和升級網域越多表示，您可以挑選較小的緩衝容量。 如果您有較多網域，則在升級和失敗期間，無法使用的叢集部分當然會比較少。 指定緩衝處理的容量意義也必須指定計量的節點容量。

如何在 ClusterManifest.xml 指定緩衝處理的容量的範例如下：

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

如何指定緩衝處理的容量 ClusterConfig.json 透過獨立部署或 Template.json Azure 託管叢集的範例如下：

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

當叢集耗盡計量的緩衝容量時，建立新的服務會失敗。 防止建立新服務以保留緩衝區，可確保升級和失敗不會造成節點超出容量。 緩衝的容量是選擇性的但我們建議在任何定義計量容量的叢集。

叢集資源管理員會公開此負載資訊。 對於每個計量，這項資訊包括： 
- 緩衝的容量設定。
- 容量總計。
- 目前的耗用量。
- 是否每個度量會被視為平衡。
- 標準差的統計資料。
- 具有最大的節點和最小負載。  
  
下列程式碼會顯示該輸出的範例：

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>後續步驟
* 在 「 叢集資源管理員 」 內的架構和資訊流程的資訊，請參閱[叢集資源管理員架構概觀](service-fabric-cluster-resource-manager-architecture.md)。
* 定義重組度量是一種方式合併而不是擴增的節點上的負載。若要了解如何設定重組，請參閱[度量的重組和 Service Fabric 中的負載](service-fabric-cluster-resource-manager-defragmentation-metrics.md)。
* 從頭開始並[認識到 Service Fabric 叢集 Resource Manager](service-fabric-cluster-resource-manager-introduction.md)。
* 若要了解叢集資源管理員如何管理和叢集中的負載平衡，請參閱[平衡 Service Fabric 叢集](service-fabric-cluster-resource-manager-balancing.md)。

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
