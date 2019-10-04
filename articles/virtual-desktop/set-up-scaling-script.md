---
title: 自動調整 Windows 虛擬桌面工作階段主機規模-Azure
description: 說明如何為 Windows 虛擬桌面工作階段主機設定自動調整腳本。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 932fbe6814df8ec324dd3360bcacfcbcf1c19b62
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71842778"
---
# <a name="scale-session-hosts-dynamically"></a>動態調整工作階段主機

針對 Azure 中的許多 Windows 虛擬桌面部署，虛擬機器成本代表總 Windows 虛擬桌面部署成本的重要部分。 若要降低成本，最好在離峰使用時間關閉和解除配置工作階段主機虛擬機器（Vm），然後在尖峰使用時間時重新開機它們。

本文使用簡單的調整腳本，在您的 Windows 虛擬桌面環境中自動調整工作階段主機虛擬機器。 若要深入瞭解調整腳本的運作方式，請參閱[調整腳本的運作方式](#how-the-scaling-script-works)一節。

## <a name="prerequisites"></a>必要條件

執行腳本的環境必須具備下列專案：

- 具有查詢該租使用者之許可權的 Windows 虛擬桌面租使用者和帳戶或服務主體（例如 RDS 參與者）。
- 已設定並向 Windows 虛擬桌面服務註冊的工作階段主機集區 Vm。
- 透過工作排程器執行排程工作的額外虛擬機器，並具有工作階段主機的網路存取權。 這在檔中稍後會以 scaler VM 的形式加以參考。
- 在執行排程工作的 VM 上安裝的[Microsoft Azure Resource Manager PowerShell 模組](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)。
- 安裝在執行排程工作之 VM 上的[Windows 虛擬桌面 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)。

## <a name="recommendations-and-limitations"></a>建議和限制

執行調整腳本時，請留意下列事項：

- 此調整腳本只能處理執行調整腳本之排程工作的每個實例的一個主機集區。
- 執行調整腳本的排程工作必須位於永遠開啟的 VM 上。
- 為調整腳本的每個實例和其設定建立個別的資料夾。
- 此腳本不支援以系統管理員身分登入，以 Windows 虛擬桌面具有需要多重要素驗證的 Azure AD 使用者帳戶。 建議您使用服務主體來存取 Windows 虛擬桌面服務和 Azure。 遵循[此教學](create-service-principal-role-powershell.md)課程，以使用 PowerShell 建立服務主體和角色指派。
- Azure 的 SLA 保證僅適用于可用性設定組中的 Vm。 檔的目前版本描述具有單一 VM 執行調整的環境，這可能不符合可用性需求。

## <a name="deploy-the-scaling-script"></a>部署調整腳本

下列程式會告訴您如何部署調整腳本。

### <a name="prepare-your-environment-for-the-scaling-script"></a>準備您的環境以進行調整腳本

首先，準備您的環境以進行調整腳本：

1. 使用網域系統管理帳戶登入將執行排程工作的 VM （scaler VM）。
2. 在 scaler VM 上建立資料夾，以保存調整腳本和其設定（例如**C： \\scaling-HostPool1**）。
3. 從[調整腳本存放庫](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script)下載**basicScale**、 **config.xml**和**Functions-PSStoredCredentials**檔案，以及**PowershellModules**資料夾，並將其複製到您在步驟2中建立的資料夾。 有兩種主要方式可在將檔案複製到 scaler VM 之前取得檔案：
    - 將 git 存放庫複製到本機電腦。
    - 查看每個檔案的**原始**版本，將每個檔案的內容複寫並貼到文字編輯器中，然後以對應的檔案名和檔案類型儲存檔案。 

### <a name="create-securely-stored-credentials"></a>建立安全儲存的認證

接下來，您必須建立安全儲存的認證：

1. 以系統管理員身分開啟 PowerShell ISE。
2. 執行下列 Cmdlet 來匯入 RDS PowerShell 模組：

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. 開啟 [編輯] 窗格並載入**Function-PSStoredCredentials**檔案，然後執行整個腳本（F5）
4. 執行下列 Cmdlet：
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    例如，**集合-變數名稱 KeyPath-Scope Global-Value "c： \\scaling-HostPool1"**
5. 執行**StoredCredential-KeyPath \$KeyPath** Cmdlet。 出現提示時，請輸入您的 Windows 虛擬桌面認證，其具有查詢主機集區的許可權（在**config.xml**中指定主機集區）。
    - 如果您使用不同的服務主體或標準帳戶，請針對每個帳戶執行**StoredCredential-KeyPath \$KeyPath** Cmdlet 一次，以建立本機儲存的認證。
6. 執行**StoredCredential-List** ，確認已成功建立認證。

### <a name="configure-the-configxml-file"></a>設定 config.xml 檔案

在下欄欄位中輸入相關的值，以更新 config.xml 中的調整腳本設定：

| 欄位                     | 描述                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | 與會話主機 Vm 執行所在的訂用帳戶相關聯的 Azure AD 租使用者識別碼     |
| AADApplicationId              | 服務主體應用程式識別碼                                                       |
| AADServicePrincipalSecret     | 這可以在測試階段輸入，但一旦您使用 Functions-PSStoredCredentials 建立認證，就會保持空白 **。**    |
| currentAzureSubscriptionId    | 工作階段主機 Vm 執行所在的 Azure 訂用帳戶識別碼                        |
| tenantName                    | Windows 虛擬桌面租使用者名稱                                                    |
| hostPoolName                  | Windows 虛擬桌面主機集區名稱                                                 |
| RDBroker                      | WVD 服務的 URL，預設值為 HTTPs： \//rdbroker. WVD. microsoft .com             |
| 使用者名稱                      | 服務主體應用程式識別碼（可能會有與 AADApplicationId 相同的服務主體）或不含多重要素驗證的標準使用者 |
| isServicePrincipal            | 接受的值為**true**或**false**。 指出所使用的第二組認證是否為服務主體或標準帳戶。 |
| BeginPeakTime                 | 當尖峰使用時間開始時                                                            |
| EndPeakTime                   | 當尖峰使用時間結束時                                                              |
| TimeDifferenceInHours         | 當地時間與 UTC 之間的時差（以小時為單位）                                   |
| SessionThresholdPerCPU        | 每個 CPU 閾值的會話數目上限，用於判斷新工作階段主機 VM 何時需要在尖峰時段啟動。  |
| MinimumNumberOfRDSH           | 主機集區 Vm 的最小數目，以在離峰使用時間繼續執行             |
| LimitSecondsToForceLogOffUser | 強制使用者登出前等待的秒數。如果設定為0，則不會強制使用者登出。  |
| LogOffMessageTitle            | 在強制登出之前傳送給使用者的訊息標題                  |
| LogOffMessageBody             | 登出之前傳送給使用者的警告訊息本文。例如，「這部電腦會在 X 分鐘內關機。 請儲存您的工作並登出。」 |

### <a name="configure-the-task-scheduler"></a>設定工作排程器

在設定 .xml 檔案之後，您必須將工作排程器設定為定期執行 basicScaler 檔案的時間。

1. 啟動**工作排程器**。
2. 在 [**工作排程器**] 視窗中，選取 [**建立**工作]。
3. 在 **[建立工作**] 對話方塊中，選取 [一般] 索引標籤，輸入**名稱**（例如，"Dynamic RDSH"），選取 **[** **不論使用者是否登入**，也要執行，並**以最高許可權執行**]。
4. 移至 [**觸發**程式] 索引標籤，然後選取 [**新增 ...** ]
5. 在 [**新增觸發**程式] 對話方塊的 [ **Advanced settings**] 底下，勾選 [**重複**工作]，然後選取適當的期間和持續時間（例如， **15 分鐘**或**無限期**）。
6. 選取 [**動作**] 索引標籤和 [**新增 ...** ]
7. 在 [**新增動作**] 對話方塊中，于 [**程式/腳本**] 欄位中輸入 [ **powershell** ]，然後在 [**新增引數（選擇性）** ] 欄位中輸入**C： \\scaling @ no__t-5basicScale。**
8. 移至 [**條件**和**設定**] 索引標籤，然後選取 **[確定]** 以接受每個的預設設定。
9. 輸入您打算執行調整腳本之系統管理帳戶的密碼。

## <a name="how-the-scaling-script-works"></a>調整腳本的運作方式

此調整腳本會從 config.xml 檔案讀取設定，包括一天內尖峰使用期間的開始和結束。

在尖峰使用時間期間，腳本會檢查目前的會話數目，以及每個主機集區目前執行的 RDSH 容量。 它會根據 config.xml 檔案中定義的 SessionThresholdPerCPU 參數，計算執行中的工作階段主機 Vm 是否有足夠的容量可支援現有的會話。 如果不是，腳本會啟動主機集區中的其他工作階段主機 Vm。

在離峰使用時間期間，腳本會根據 config.xml 檔案中的 MinimumNumberOfRDSH 參數，決定應關閉哪些工作階段主機 Vm。 腳本會將工作階段主機 Vm 設定為清空模式，以防止新會話連接到主機。 如果您將 config.xml 檔案中的**LimitSecondsToForceLogOffUser**參數設定為非零的正值，腳本會通知任何目前已登入的使用者儲存工作、等候設定的時間量，然後強制使用者登出。在工作階段主機 VM 上登出所有使用者會話之後，腳本將會關閉伺服器。

如果您將 config.xml 檔案中的**LimitSecondsToForceLogOffUser**參數設定為零，則腳本會允許主機集區內容中的會話設定，處理登出使用者會話。 如果工作階段主機 VM 上有任何會話，它會讓工作階段主機 VM 保持執行狀態。 如果沒有任何會話，腳本將會關閉工作階段主機 VM。

此腳本是設計為使用工作排程器在 scaler VM 伺服器上定期執行。 根據您遠端桌面服務環境的大小來選取適當的時間間隔，並記住啟動和關閉虛擬機器可能需要一些時間。 建議您每隔15分鐘執行調整腳本。

## <a name="log-files"></a>記錄檔

調整腳本會建立兩個記錄檔： **WVDTenantScale**和**WVDTenantUsage**。 **WVDTenantScale**會在每次執行調整腳本時記錄事件和錯誤（如果有的話）。

**WVDTenantUsage**會在您每次執行調整腳本時，記錄作用中的核心數目和作用中的虛擬機器數目。 您可以使用這項資訊來預估 Microsoft Azure Vm 的實際使用量和成本。 檔案會格式化為逗號分隔值，每個專案都包含下列資訊：

>時間、主機集區、核心、Vm

檔案名也可以修改為擁有 .csv 副檔名、載入 Microsoft Excel，然後進行分析。
