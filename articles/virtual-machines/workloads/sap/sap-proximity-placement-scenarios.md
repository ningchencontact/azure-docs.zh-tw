---
title: 適用于 SAP 應用程式之最佳網路延遲的 Azure 鄰近放置群組 |Microsoft Docs
description: 說明使用 Azure 鄰近放置群組的 SAP 部署案例
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
ms.openlocfilehash: 02dcb7174dd9cb2926ef2fafda4b521b939ae68a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077978"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>適用於最佳網路延遲的 Azure Proximity Placement Groups (搭配 SAP 應用程式)
以 SAP NetWeaver 或 SAP S/4HANA 架構為基礎的 SAP 應用程式, 對於 SAP 應用層與 SAP 資料庫層之間的網路延遲是敏感的。 這些架構的敏感性原因在於, 大部分商務邏輯都是在應用層中執行。 執行商務邏輯的結果是, SAP 應用層會以高頻率 (每秒數千和數萬個) 發出查詢至資料庫層。 在大部分情況下, 這些查詢的本質很簡單。 而且通常可以在不到500毫秒或更少的情況中, 于資料庫層上執行。 從應用層將這類查詢傳送至資料庫層, 並從資料庫層接收結果集的時間, 對於執行商務程式所花費的時間有重大影響。 這對網路延遲的敏感性是需要花費在 SAP 部署專案中的原因, 以達到最佳的網路延遲。 在[SAP 附注 #1100926-常見問題:網路效能](https://launchpad.support.sap.com/#/notes/1100926/E), SAP 發佈了如何分類網路延遲的一些指導方針。

一側, 在許多 Azure 區域中, 資料中心的數量成長, 同時也會由可用性區域的引進所觸發。 相反地, 客戶 (特別是高階 SAP 系統) 在 M 系列系列或 HANA 大型實例中使用更多特殊的 VM Sku。 不存在於特定 Azure 區域中所有資料中心內的 Azure 虛擬機器類型。 由於這兩個喜好傾向的結果, 客戶遇到了一種情況, 亦即網路延遲不在最佳範圍內, 而在某些情況下, 會導致 SAP 系統的效能不佳。

為了避免這類問題, Azure 提供稱為「 [Azure 鄰近性放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)」的結構。 這種新功能已用來部署各種不同的 SAP 系統。 請檢查參考的文章, 以瞭解鄰近性放置群組的限制。 本文將涵蓋可使用或應使用 Azure 鄰近放置群組的不同 SAP 案例。

## <a name="what-are-proximity-placement-groups"></a>什麼是鄰近放置群組 
Azure 鄰近放置群組是邏輯結構, 在定義階段會系結至 Azure 區域和 Azure 資源群組。 在 Vm 部署期間, 會參考鄰近放置群組:

- 第一個已部署的 Azure VM, 以在資料中心上進行結算。 第一部虛擬機器可視為錨點 VM, 其部署在以 Azure 配置演算法為基礎的資料中心, 最後與特定 Azure 可用性區域的使用者定義結合。
- 所有後續部署的 Vm, 都會參考鄰近放置群組, 將所有後續部署的 Azure Vm 放在與第一部虛擬機器相同的資料中心內。

> [!NOTE]
> 如果未部署可在第一個 VM 所在的相同資料中心內執行特定 VM 類型的主機硬體, 則要求的 VM 類型部署將會失敗, 且會以失敗訊息結尾。 這些情況可能是較多非主流 Vm 的情況, 例如具有 Gpu 或 HPC VM 類型的虛擬機器, 其中心應為, 例如已部署為第一個 VM 類型的 M 系列 VM

單一[Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)可以有多個指派給本身的鄰近放置群組。 不過, 一個鄰近放置群組只能指派給一個 Azure 資源群組。

使用鄰近放置群組時, 您應該注意下列事項:

- 當您為 SAP 系統提供最佳效能, 並使用鄰近放置群組將自己限制為此系統的單一 Azure 資料中心時, 您可能無法將這類鄰近放置群組中所有類型的 VM 系列結合在一起。 原因是, 獨佔執行特定 VM 類型所需的特定主機硬體可能不會出現在您的「錨點 VM」已部署位置群組的資料中心內
- 在這類 SAP 系統的生命週期中, 您可能會被迫將系統移至另一個資料中心。 在您決定向外延展 HANA DBMS 層 (例如, 從四個節點移至16個節點) 時, 可能會強制執行這類移動。 但是, 沒有足夠的容量可取得您已在相同資料中心內使用的其他12個類型 Vm。
- 由於硬體解除委任, Microsoft 可能會針對您在另一個資料中心使用的 VM 類型 (而不是相同的資料中心) 建立容量。 這種情況可能表示您將鄰近放置群組的所有 Vm 移到另一個資料中心。


## <a name="azure-proximity-placement-groups-with-sap-systems-using-azure-vms-exclusively"></a>Azure 鄰近放置群組與使用 Azure Vm 的 SAP 系統獨佔
在 Azure 上, SAP NetWeaver 和 S/4HANA 系統部署的多數案例不會使用[HANA 大型實例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)。 針對這類系統的部署, 請務必在 SAP 應用層與 DBMS 層之間提供最理想的效能。 若要這麼做, 您可以為此系統定義僅適用于 Azure 的鄰近位置群組。 在大部分的客戶部署中, 客戶選擇建立適用于 SAP 系統的單一[Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)。 在這種情況下, 會有1:1 關聯性, 例如生產 ERP 系統資源群組及其鄰近放置群組。 在某些其他部署案例中, 客戶會以水準方式組織其資源群組, 並收集單一資源群組中的所有生產系統。 在這種情況下, 生產環境 SAP 系統的資源群組與生產 SAP ERP、SAP BW 等的數個鄰近放置群組之間, 會有1對多關聯性。應避免在單一鄰近放置群組中, 將數個或甚至是所有 SAP 生產或非生產系統進行捆綁。 在少數 SAP 系統或 SAP 系統和一些周圍應用程式需要低延遲網路通訊的例外狀況中, 您可以考慮將這些系統移至一個鄰近放置群組。 建議的原因是, 您在鄰近放置群組中分組的系統越多, 機率就愈高:

- 您需要的 VM 類型無法在鄰近放置群組錨定的特定資料中心內執行。
- 當您在一段時間內將其他軟體新增至現有的鄰近放置群組時, 不會再完成某些非主流 Vm (例如 M 系列) 的資源。

如所述, 理想的用法如下所示:

![所有 Azure Vm 的鄰近 placemen 群組](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

在此情況下, 單一 SAP 系統會群組在一個資源群組中, 每個都有一個鄰近放置群組。 無論您使用的是 HANA 向外延展或 DBMS 相應增加設定, 都不會有任何相關性。


## <a name="azure-proximity-placement-groups-and-hana-large-instances"></a>Azure 鄰近放置群組和 HANA 大型實例
在某些 SAP 系統依賴[Hana 大型](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)實例作為應用層的情況下, 當您使用已有的 Hana 大型實例單位時, 可以達到 Hana 大型實例單位與 Azure vm 之間的網路延遲嚴重改進已部署在[修訂4的資料列或戳記](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)中。 其中一項改良功能是 HANA 大型實例單位 (如已部署) 會取得已部署的鄰近放置群組。 您可以使用該鄰近放置群組來部署您的應用層 Vm。 因此, 這些 Vm 將會部署在裝載您的 HANA 大型實例單位的相同資料中心內。

若要偵測您的 HANA 大型實例單位是否部署在修訂4戳記或資料列中, 請參閱[AZURE HANA 大型實例控制的 Azure 入口網站](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#look-at-attributes-of-single-hli-unit)一文。 在您的 HANA 大型實例單位的屬性總覽中, 您也可以找出近接位置群組的名稱, 因為它在部署階段是針對您的 HANA 大型實例單位所建立。 [屬性總覽] 中顯示的名稱是鄰近放置群組的名稱, 您應該使用將應用層 Vm 部署到。

相對於僅使用 Azure 虛擬機器的 SAP 系統, 使用 HANA 大型實例時, 應該使用多少個[azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal), 而不是您的角度。 [Hana 大型實例租](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-know-terms)使用者的所有「Hana 大型實例」單位都會分組在單一 Azure 資源群組中, 如[這裡](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#display-of-hana-large-instance-units-in-the-azure-portal)所述。 除非您想要部署到不同的租使用者, 例如生產環境和非生產環境或特定系統, 否則您所有的 HANA 大型實例單位都會部署在一個 HANA 大型實例租使用者中, 這同樣與 Azure 有1:1 關聯性資源群組。 而所有的單一單位都會定義個別的鄰近放置群組。 

因此, 單一租使用者的 Azure 資源群組和鄰近放置群組之間的群組將會如下所示:

![所有 Azure Vm 的鄰近放置群組](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)


## <a name="short-example-of-deploying-with-azure-proximity-placement-groups"></a>使用 Azure 鄰近放置群組進行部署的簡短範例
若要示範如何使用 Azure 鄰近放置群組來部署您的 VM, 請參閱這裡的 PowerShell 命令清單, 其中會示範使用 Azure 鄰近放置群組的第一個小練習

使用您的[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)登入後的第一個步驟, 就是使用命令來檢查您是否位於您想要用來部署的正確 Azure 訂用帳戶中:

<pre><code>
Get-AzureRmContext
</code></pre>

如果您需要變更為不同的訂用帳戶, 您可以藉由執行下列命令來執行此動作:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

第三個步驟中, 您想要使用下列命令來建立新的 Azure 資源群組:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

您現在可以建立新的鄰近放置群組, 如下所示:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

現在您可以使用類似的命令, 開始將您的第一個 VM 部署到這個鄰近放置群組:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

在上述命令中, 會部署以 Windows 為基礎的 VM。 在此 VM 部署成功之後, 鄰近位置群組的資料中心範圍會在 Azure 區域內定義。 所有參考鄰近位置群組的後續 VM 部署, 如最後一個命令, 將會部署在相同的 Azure 資料中心, 只要 VM 類型可以裝載于放置於該資料中心的硬體上, 並可使用 (或) 該 VM 類型的容量。

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>結合可用性設定組和可用性區域與鄰近性放置群組 
使用可用性區域進行 SAP 系統部署時, 其中一個缺點是無法使用特定區域內的可用性設定組來控制 SAP 應用層。 由於您想要在與 DBMS 層相同的區域中部署 SAP 應用層, 且不支援部署單一 VM 時參考可用性區域和可用性設定組, 因此您已強制透過參考區域來部署應用層因此, 會失去確保應用層 Vm 散佈在不同更新和失敗網域的功能。 有了鄰近放置群組的協助, 您就可以克服這項限制。 部署的順序如下所示:

- 建立鄰近放置群組
- 藉由參考特定的 Azure 可用性區域, 部署您的「錨點 VM」, 通常是 DBMS 伺服器
- 建立參考 Azure 鄰近性群組的可用性設定組 (請參閱下文)
- 藉由參考可用性設定組和鄰近放置群組來部署應用層 Vm

如上面所示, 您會在部署 VM 時參考 Azure 可用性區域和鄰近位置群組, 而不是部署第一個 VM, 例如:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

若要成功部署此虛擬機器, 以在一個 Azure 可用性區域中裝載 SAP 系統的資料庫實例, 鄰近放置群組的範圍固定為其中一個資料中心, 其中代表您所定義的可用性區域.

我們假設您將中央服務 Vm 部署為與 DBMs Vm 相同的方式, 方法是參考 DBMS Vm 和相同鄰近放置群組的相同區域。 在下一個步驟中, 您必須建立要用於 SAP 系統之應用層的可用性設定組。
必須定義和建立鄰近放置群組。 建立可用性設定組的命令需要對鄰近放置群組識別碼 (而非名稱) 進行額外的參考。 您可以使用下列方式取得鄰近放置群組的識別碼:



<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

當您建立可用性設定組時, 如果您使用受控磁片, 則需要考慮其他參數 (除非指定不同的預設值) 和鄰近放置群組:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

在理想的情況下, 您應該使用三個容錯網域。 不過, 支援的容錯網域數目會因區域而異。 在此情況下, 特定區域可能會有兩個最大的容錯網域計數。 若要部署您的應用層 Vm, 您需要新增可用性設定組名稱和鄰近放置組名的參考, 如下所示:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

此順序的最終結果將是位於特定可用性區域的 SAP 系統的 DBMS 層和中央服務, 以及在與 DBMS VM 相同的 Azure 資料中心內, 透過可用性設定組找到的 SAP 應用層。技術.

> [!NOTE]
> 當您將一個 DBMS VM 部署到一個區域, 並將第二個 DBMS VM 部署到另一個區域以建立高可用性設定時, 您會針對每個區域要求不同的鄰近放置群組。 針對您可能會使用的可用性設定組, 相同的 si 為 true

## <a name="get-an-existing-system-into-azure-proximity-placement-groups"></a>將現有的系統放入 Azure 鄰近放置群組
當您已部署 SAP 系統時, 您可能會想要將某些重要系統的網路延遲優化, 並在相同的資料中心內找出應用層和 DBMS 層。 在近接位置群組功能的公開預覽階段中, 需要刪除 Vm 和新建立的 Vm, 才能執行這類移動到鄰近放置群組。 在此功能的這個階段, 關閉 Vm 以將這些關閉虛擬機器指派給鄰近放置群組並沒有足夠的能力。


## <a name="next-steps"></a>後續步驟
參閱文件：

- [Azure 上的 SAP 工作負載規劃和部署檢查清單](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- [預覽：使用 Azure CLI 將 Vm 部署至鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/linux/proximity-placement-groups)
- [預覽：使用 PowerShell 將 Vm 部署至鄰近放置群組](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups)
- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

