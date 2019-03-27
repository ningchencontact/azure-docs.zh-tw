---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484860"
---
根據您的環境和選擇，指令碼可建立所有的叢集基礎結構，包括 Azure 虛擬網路、儲存體帳戶、雲端服務、網域控制站、遠端或本機 SQL Database、前端節點、及其他叢集節點。 或者，该脚本可以使用预先存在的 Azure 基础结构仅创建 HPC 群集节点。

如需規劃 HPC Pack 2012 R2 叢集的背景資訊，請參閱 HPC Pack TechNet 文件庫中的[產品評估及規劃](https://technet.microsoft.com/library/jj899596.aspx)和[快速入門](https://technet.microsoft.com/library/jj899590.aspx)內容。

## <a name="prerequisites"></a>必要條件
* **Azure 訂用帳戶**：您可以在 「 Azure 全球 」 或 Azure China 服務中使用的訂用帳戶。 您的訂用帳戶限制將會影響到您可以部署的叢集節點類型與數量。 如需相關資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../articles/azure-subscription-service-limits.md)。
* **Windows 用戶端電腦使用 Azure PowerShell 0.8.10 或更新版本安裝並設定**:請參閱[開始使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)安裝指示和步驟，連接到您的 Azure 訂用帳戶。
* **HPC Pack IaaS 部署指令碼**:下載並解壓縮最新版的指令碼[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)。 執行 `New-HPCIaaSCluster.ps1 –Version`以檢查指令碼的版本。 這篇文章是根據 4.5.2 版的指令碼撰寫的。
* **指令碼組態檔**:建立 XML 檔案的指令碼用來設定 HPC 叢集。 如需相關資訊和範例，請參閱本文稍後的章節以及部署指令碼隨附的 Manual.rtf 檔。

## <a name="syntax"></a>語法
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> 以系統管理員身分執行指令碼。
> 
> 

### <a name="parameters"></a>參數
* **ConfigFile**:指定說明 HPC 叢集之組態檔的檔案路徑。 詳細資訊請參閱本主題中的組態檔，或指令碼所在之資料夾中的 Manual.rtf 檔。
* **AdminUserName**:指定使用者名稱。 如果網域樹系是由指令碼所建立，這將會成為所有 VM 的本機系統管理員使用者名稱和網域系統管理員名稱。 如果網域樹系已存在，則會將網域使用者指定為安裝 HPC Pack 的本機系統管理員使用者名稱。
* **AdminPassword**:指定系統管理員的密碼。 如果未在命令列中指定，指令碼會提示您輸入密碼。
* **HPCImageName** （選擇性）：指定用來部署 HPC 叢集的 HPC Pack VM 映像名稱。 它必須是 Microsoft 在 Azure Marketplace 中提供的 HPC Pack 映像。 如果未指定 (通常會建議使用)，指令碼會選擇最新發佈的 [HPC Pack 2012 R2 映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)。 最新的映像是基於已安裝 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。
  
  > [!NOTE]
  > 若未指定有效的 HPC Pack 映像，部署將會失敗。
  > 
  > 
* **LogFile** （選擇性）：指定部署記錄檔路徑。 若未指定，指令碼會在執行指令碼之電腦的暫存目錄中建立記錄檔。
* **強制**（選擇性）：隱藏所有確認提示。
* **NoCleanOnFailure** （選擇性）：指定未成功部署 Azure Vm 不會移除。 請先手動移除這些 VM 才能重新執行指令碼以繼續部署，否則部署可能會失敗。
* **PSSessionSkipCACheck** （選擇性）：每個雲端服務使用此指令碼部署的 Vm，Azure，自動產生自我簽署的憑證，並在雲端服務中的所有 Vm 會都使用此憑證做為預設 Windows 遠端管理 (WinRM) 憑證。 為了在這些 Azure VM 中部署 HPC 功能，指令碼會依預設將這些憑證暫時安裝在用戶端電腦的「本機電腦\\信任的根憑證授權單位」存放區中，以抑制指令碼執行期間的「不受信任的 CA」安全性錯誤； 指令碼完成時即會移除憑證。 如果指定此参数，则不会在客户端计算机中安装证书，并且会隐藏安全警告。
  
  > [!IMPORTANT]
  > 此參數不建議用於生產部署。
  > 
  > 

### <a name="example"></a>範例
下列範例會使用組態檔 *MyConfigFile.xml*建立新的 HPC Pack 叢集，並指定用來安裝叢集的管理員認證。

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>其他考量
* 该脚本可以选择性地启用通过 HPC Pack Web 门户或 HPC Pack REST API 提交作业。
* 如果您想要安裝其他軟體或進行其他設定，指令碼可以選擇性地在前端節點上執行自訂的前置和後置組態指令碼。

## <a name="configuration-file"></a>組態檔
部署指令碼的組態檔是 XML 檔案。 結構描述檔案 HPCIaaSClusterConfig.xsd 位於 HPC Pack IaaS 部署指令碼資料夾中。 **IaaSClusterConfig** 是組態檔的根元素，其中包含相關子元素用以說明部署指令碼資料夾中的檔案 Manual.rtf。

