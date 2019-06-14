---
title: Azure Site Recovery 部署規劃工具版本歷程記錄
description: 已知的不同站台復原部署規劃工具版本修正和已知的限制，以及其發行日期。
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 04/24/2019
ms.author: dapatil
ms.openlocfilehash: 2edf7ce3be1402a497ceab5b826a89ee43c5c39b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927368"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Azure Site Recovery 部署規劃工具版本歷程記錄

這篇文章提供 Azure Site Recovery Deployment Planner 修正，已知在每個的限制和其發行日期以及所有版本的歷程的記錄。

## <a name="version-24"></a>2\.4 版

**發行日期：2019 年 4 月 17日日**

**修正：**

- 改進操作系統的相容性，特別是當處理當地語系化的錯誤時。
- 新增的 Vm 最多 20 Mbps 的資料變更率 （變換） 相容性檢查清單。
- 改良的錯誤訊息
  - 新增的 vCenter 6.7 的支援。
  - 已新增的支援 Windows Server 2019 和 Red Hat Enterprise Linux (RHEL) 的工作站。



## <a name="version-23"></a>2\.3 版

**發行日期：2018 年 12 月 3日日**

**修正：**

- 已修正防止部署規劃工具產生報告，以使用提供的目標位置和訂用帳戶的問題。

## <a name="version-22"></a>2\.2 版 

**發行日期：2018 年 4 月 25日日**

**修正：**

- GetVMList 作業：
  - 已修正導致 GetVMList 指定的資料夾不存在，則失敗的問題。 它現在會建立預設的目錄，或建立 outputfile 參數中指定的目錄。
  - 加入更詳細的 GetVMList 的失敗原因。
- 部署規劃工具報告的相容的 Vm 工作表中的資料行加入的 VM 類型資訊。
- Hyper-v 至 Azure 的災害復原：
  - 具有共用 Vhd 和傳遞的排除的 Vm 磁碟從程式碼剖析。 Startprofiling 作業會顯示在主控台中排除的 Vm 清單。
  - 已新增到不相容的 Vm 清單超過 64 個磁碟的 Vm。
  - 更新的初始複寫 (IR) 和差異複寫 (DR) 壓縮因數。
  - 新增的 SMB 存放區的有限的支援。

## <a name="version-21"></a>2\.1 版

**發行日期：2018 年 1 月 3日日**

**修正：**

- 更新 Excel 報表。
- GetThroughput 作業中已修正的 bug。
- 已新增的選項來限制程式碼剖析或產生報表的 Vm 數目。 預設限制為 1,000 個 Vm。
- VMware 至 Azure 的災害復原：
  - 已修正的問題，Windows Server 2016 VM 進入不相容的資料表。 
  - 可延伸韌體介面 (EFI) 的 Windows Vm 的更新相容性訊息。
- 更新 VMware 至 Azure 和 HYPER-V 到 Azure，VM 資料變換限制每個 VM。 
- 剖析的 VM 清單檔案的更高的可靠性。

## <a name="version-201"></a>版本 2.0.1

**發行日期：2017 年 12 月 7日日**

**修正：**

- 已新增的建議，以最佳化網路頻寬。

## <a name="version-20"></a>2\.0 版

**發行日期：2017 年 11 月 28日日**

**修正：**

- 已新增的支援 HYPER-V 到 Azure 的災害復原。
- 已新增的成本計算機。
- 新增的作業系統版本檢查適用於 VMware 到 Azure 的災害復原，以判斷 VM 是否相容或相容的保護。 此工具會使用由 vCenter 伺服器的 vm 的 OS 版本字串。 這是客體作業系統版本在 VMware 中建立 VM 時選取該使用者。

**已知的限制：**

- HYPER-V 至 Azure 的災害復原、 VM 名稱包含的字元像是： `,`， `"`， `[`， `]`，和``` ` ```不支援。 若剖析，產生報告將會失敗，或將會有不正確的結果。
- 適用於 VMware 到 Azure 的災害復原，不支援名稱包含逗號的 VM。 若剖析，報告產生失敗，或將會有不正確的結果。

## <a name="version-131"></a>1\.3.1 版

**發行日期：2017 年 7 月 19日日** 

**修正：**

- 已新增的支援大型磁碟 (> 1TB)，在產生報告。 現在您可以使用部署規劃工具來規劃磁碟大小大於 1 TB 的虛擬機器的複寫 (最多 4095 GB)。
深入了解 [Azure Site Recovery 中的大型磁碟支援](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>1\.3 版

**發行日期：2017 年 9 日**

**修正：**

- 已新增的支援在報表產生的受控磁碟。 可以放到單一儲存體帳戶的 Vm 數目是根據計算如果容錯移轉/測試容錯移轉選取受控的磁碟。

## <a name="version-12"></a>1\.2 版

**發行日期：2017 年 4 月 7日日**

**修正：**

- 已新增的開機類型 （BIOS 或 EFI） 檢查每個 VM，以判斷 VM 是否相容或相容的保護。
- 已新增的 OS 類型相容的 Vm 和不相容的 Vm 工作表中的每個虛擬機器的資訊。
- 已新增的支援 GetThroughput 作業在美國政府和中國 Microsoft Azure 區域。
- 已針對 vCenter 和 ESXi 伺服器新增更多必要條件檢查。
- 修正不正確的報表設定為非英文地區設定時，正在產生的問題。

## <a name="version-11"></a>1\.1 版

**發行日期：2017 年 3 月 9日日**

**修正：**

- 已修正防止在各種 vCenter ESXi 主機有兩個或多個具有相同名稱或 IP 位址的 Vm 時，程式碼剖析 Vm 的問題。
- 修正造成複製和搜尋要停用相容的 Vm 和不相容的 Vm 工作表的問題。

## <a name="version-10"></a>1\.0 版

**發行日期：2017 年 2 月 23日日**

**已知的限制：**

- 只會針對 VMware 到 Azure 的災害復原案例的支援。 適用於 HYPER-V 到 Azure 的災害復原案例，使用[HYPER-V 容量規劃工具](./site-recovery-capacity-planning-for-hyper-v-replication.md)。
- 不支援 GetThroughput 作業在美國政府和中國 Microsoft Azure 區域。
- 工具的 cann't 設定檔如果 vCenter server 在各種 ESXi 主機有兩個以上的 Vm 具有相同名稱或 IP 位址的 Vm。
在此版本中，此工具會略過 VMListFile 中重複 VM 名稱或 IP 位址的分析。 因應措施是使用 ESXi 主機 (而非 vCenter Server) 剖析 VM。 請務必為每部 ESXi 主機執行一個執行個體。
