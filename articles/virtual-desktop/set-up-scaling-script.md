---
title: 自動調整 Windows 虛擬桌面預覽工作階段主機-Azure
description: 描述如何設定 Windows 虛擬桌面預覽工作階段主機的自動調整指令碼。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: e9f500e3ab965b9dbfc5e395a6572497c85f6f8f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755141"
---
# <a name="automatically-scale-session-hosts"></a>自動調整工作階段主機

許多 Windows 虛擬桌面預覽 Azure 部署中，虛擬機器費用則代表總 Windows 虛擬桌面部署成本的重要部分。 為了降低成本，最好是以關閉和解除配置的工作階段主機虛擬機器 (Vm) 在離峰的使用量時段，然後重新啟動這些尖峰使用時間。

本文章會使用簡單的調整指令碼來自動調整 Windows 虛擬桌面環境中的 工作階段主機的虛擬機器。 若要深入了解調整指令碼的運作方式，請參閱[調整指令碼的運作方式](#how-the-scaling-script-works)一節。

## <a name="prerequisites"></a>必要條件

您用來執行指令碼的環境必須具有下列動作：

- Windows 虛擬桌面的租用戶和帳戶或查詢 （例如 RDS 參與者） 該租用戶的權限的服務主體。
- 工作階段主應用程式集區 Vm 設定和註冊的 Windows 虛擬桌面服務。
- 其他虛擬機器透過工作排程器執行排定的工作，並具有工作階段主機的網路存取權。 這會被指文件中稍後 scaler VM。
- [Microsoft Azure Resource Manager PowerShell 模組](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)執行排定的工作在 VM 上安裝。
- [Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)執行排定的工作在 VM 上安裝。

## <a name="recommendations-and-limitations"></a>建議與限制

執行時調整指令碼，請記住下列事項：

- 此調整指令碼只能處理一個主應用程式集區，每個執行個體正在執行調整指令碼之排程工作。
- 在排程的工作執行調整指令碼必須一律位於 VM 上。
- 建立個別的資料夾，每個執行個體調整指令碼和其組態。
- 此指令碼不支援使用 Azure AD 使用者帳戶需要多重要素驗證的 Windows 虛擬桌面系統管理員身分登入。 我們建議您用以存取 Windows 的虛擬桌面服務和 Azure 服務主體。 請遵循[本教學課程](create-service-principal-role-powershell.md)使用 PowerShell 建立服務主體和角色指派。
- Azure 的 SLA 保證只適用於可用性設定組中的 Vm。 目前版本的文件會描述環境進行調整，單一 vm 可能不符合可用性需求。

## <a name="deploy-the-scaling-script"></a>部署調整指令碼

下列程序會告訴您如何部署調整指令碼。

### <a name="prepare-your-environment-for-the-scaling-script"></a>準備您的環境調整指令碼

首先，準備您的環境調整指令碼：

1. 登入將會執行排定的工作，以網域系統管理帳戶的 VM (scaler VM)。
2. Scaler 來保存調整指令碼和其組態的 VM 上建立資料夾 (例如**c:\\調整 HostPool1**)。
3. 下載**basicScale.ps1**， **Config.xml**，並**函式 PSStoredCredentials.ps1**檔案，而**PowershellModules**從資料夾[調整指令碼存放庫](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script)並將它們複製到您在步驟 2 中建立的資料夾。 有兩種主要的方式，才能將它們複製至 scaler VM 取得這些檔案：
    - Git 存放庫複製到本機電腦。
    - 檢視**Raw**版本的每個檔案，複製並貼上到文字編輯器中，每個檔案的內容，然後儲存檔案與檔案類型與對應的檔案名稱。 

### <a name="create-securely-stored-credentials"></a>建立安全存放的認證

接下來，您必須建立安全存放的認證：

1. 開啟以系統管理員的 PowerShell ISE。
2. 執行下列 cmdlet 來匯入 RDS PowerShell 模組：

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. 開啟編輯 窗格和負載**函式 PSStoredCredentials.ps1**檔案。
4. 运行以下 cmdlet：
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    例如， **Set-variable-名稱 KeyPath-全域範圍-值"c:\\調整 HostPool1"**
5. 執行**新增 StoredCredential KeyPath \$KeyPath** cmdlet。 出現提示時，輸入您的 Windows 虛擬桌面認證查詢的主應用程式集區的權限 (主應用程式集區中指定**config.xml**)。
    - 如果您使用不同的服務主體或標準帳戶，執行**新增 StoredCredential KeyPath \$KeyPath**一旦針對每個帳戶，來建立本機儲存認證的 cmdlet。
6. 執行**Get StoredCredentials-清單**，確認已成功建立認證。

### <a name="configure-the-configxml-file"></a>設定 config.xml 檔案

要更新調整指令碼設定在 config.xml 中的下列欄位中輸入相關的值：

| 欄位                     | 描述                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | 將工作階段主機的 Vm 執行所在的訂用帳戶相關聯的 azure AD 租用戶識別碼     |
| AADApplicationId              | 服務主體的應用程式識別碼                                                       |
| AADServicePrincipalSecret     | 這可以在測試階段輸入，但要保留空白，一旦您建立具有認證是**函式 PSStoredCredentials.ps1**    |
| currentAzureSubscriptionId    | 工作階段主機的 Vm 執行所在的 Azure 訂用帳戶的識別碼                        |
| tenantName                    | Windows 虛擬桌面的租用戶名稱                                                    |
| hostPoolName                  | Windows 虛擬桌面主應用程式集區名稱                                                 |
| RDBroker                      | URL WVD 服務時，預設值，https:\//rdbroker.wvd.microsoft.com             |
| 使用者名稱                      | （它是可以在 AADApplicationId 中有相同的服務主體） 的服務主體的應用程式識別碼或標準使用者不經過 multi-factor authentication |
| isServicePrincipal            | 接受的值為**真**或是**false**。 表示第二組所使用的認證是否為服務主體 」 或 「 標準帳戶。 |
| BeginPeakTime                 | 尖峰使用時間的開始時                                                            |
| EndPeakTime                   | 尖峰使用時間結束時                                                              |
| TimeDifferenceInHours         | 當地時間與 UTC 之間的時間差異以小時為單位                                   |
| SessionThresholdPerCPU        | 每一 CPU 閾值，用來判斷何時需要新的工作階段主機 VM 在尖峰時間內啟動的工作階段數目上限。  |
| MinimumNumberOfRDSH           | 主應用程式集區在離峰的使用量期間保持執行的 Vm 數目下限             |
| LimitSecondsToForceLogOffUser | 強制使用者登出之前要等待的秒數。如果設定為 0，使用者不會強制其登出。  |
| LogOffMessageTitle            | 訊息傳送給使用者，他們要強制登出之前的標題                  |
| LogOffMessageBody             | 正在登出之前，傳送給使用者的警告訊息的主體。比方說，「 這部電腦將會關閉 X 分鐘的清單中。 請儲存您的工作，然後登出。 」 |

### <a name="configure-the-task-scheduler"></a>設定工作排程器

設定組態.xml 檔案之後, 您必須設定工作排程器定期執行 basicScaler.ps1 檔案。

1. 開始**工作排程器**。
2. 在 **工作排程器**視窗中，選取**建立工作...**
3. 在 **建立的工作**對話方塊中，選取**一般**索引標籤上，輸入**名稱**（例如，「 動態 RDSH"），選取 **不論使用者登入與否均執行**並**具有最高的權限執行**。
4. 移至**觸發程序**索引標籤，然後選取 **新增...**
5. 在**新增觸發程序** 對話方塊底下**進階設定**，檢查**重複工作每個**，然後選取適當的期限和持續時間 (比方說， **15分鐘**或是**無限期地**)。
6. 選取 **動作**索引標籤和**新增...**
7. 在 [**新的動作**] 對話方塊中，輸入**powershell.exe**到**程式或指令碼**欄位，然後輸入**c:\\調整\\RDSScaler.ps1**成**新增引數 （選擇性）** 欄位。
8. 移至**條件**並**設定**索引標籤，然後選取 **[確定]** 接受每個預設設定。
9. 輸入系統管理帳戶的密碼，您打算執行調整指令碼。

## <a name="how-the-scaling-script-works"></a>調整指令碼的運作方式

此調整指令碼會讀取 config.xml 檔案，包括的開始和結束的當天的尖峰使用量期間中的設定。

在尖峰使用期間，指令碼會檢查目前的工作階段與每個主應用程式集區的目前執行 RDSH 容量的數目。 它會計算是否執行的工作階段主機 Vm 有足夠的容量，以支援 config.xml 檔案中所定義之 SessionThresholdPerCPU 參數為基礎的現有工作階段。 如果沒有，則指令碼會啟動額外的工作階段主機 Vm 主應用程式集區中。

離峰的使用量期間，指令碼會判斷哪些 Vm 應關閉 MinimumNumberOfRDSH 參數中的 config.xml 檔案為基礎的工作階段主機。 清空模式，以避免新的工作階段連接到主機的主機 Vm 時，指令碼會設定工作階段。 如果您設定**LimitSecondsToForceLogOffUser** config.xml 檔案以非零的正整數值中的參數，指令碼將會通知任何目前已登入儲存的工作，等待已設定的一段時間，然後強制的使用者若要登出使用者。一旦工作階段主機 VM 上有已登出所有使用者工作階段，指令碼將會關閉伺服器。

如果您設定**LimitSecondsToForceLogOffUser**為零的 config.xml 檔案中的參數，指令碼可讓主應用程式中的工作階段組態設定集區屬性，以處理簽署登出使用者工作階段。 如果工作階段主機 VM 上有任何工作階段，它將保留在工作階段主機 VM 執行。 如果沒有任何工作階段，指令碼將會關閉工作階段主機 VM。

指令碼可使用工作排程器在 scaler VM 伺服器上定期執行。 選取 遠端桌面服務環境的大小為基礎的適當的時間間隔，請記得啟動和關閉虛擬機器可能需要一些時間。 建議您執行調整指令碼每隔 15 分鐘。

## <a name="log-files"></a>記錄檔

調整的指令碼會建立兩個記錄檔**WVDTenantScale.log**並**WVDTenantUsage.log**。 **WVDTenantScale.log**檔案會記錄的事件和錯誤 （如果有的話） 調整的指令碼每次執行期間。

**WVDTenantUsage.log**檔案會記錄使用中的核心數目和作用中的虛擬機器數目每次您執行調整指令碼。 您可以使用這項資訊來預估 Microsoft Azure Vm 和成本的實際使用量。 檔案會格式化為逗號分隔的值，每個項目包含下列資訊：

>時間、 主應用程式集區、 Vm 的核心

檔案名稱也可以修改以.csv 副檔名，載入到 Microsoft Excel，並加以分析。
