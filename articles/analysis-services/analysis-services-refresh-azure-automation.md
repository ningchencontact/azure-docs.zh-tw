---
title: 重新整理 Azure Analysis Services 模型，使用 Azure 自動化 |Microsoft Docs
description: 了解如何使用 Azure 自動化的程式碼模型重新整理。
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357006"
---
# <a name="refresh-with-azure-automation"></a>使用 Azure 自動化重新整理

藉由使用 Azure 自動化和 PowerShell Runbook，您可以執行自動的資料重新整理作業，在您的 Azure 分析表格式模型。  

這篇文章中的範例會使用[PowerShell SqlServer 模組](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)。

在本文稍後會提供範例為 PowerShell Runbook，可示範如何重新整理模型。  

## <a name="authentication"></a>Authentication

所有呼叫必須使用有效的 Azure Active Directory (OAuth 2) 權杖進行都驗證。  這篇文章中的範例會使用服務主體 (SPN)，來向 Azure Analysis Services。

若要深入了解建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="prerequisites"></a>必要條件

> [!IMPORTANT]
> 下列範例假設 Azure Analysis Services 防火牆停用。 如果已啟用防火牆，要求起始端的公用 IP 位址必須設為在防火牆中的允許清單。

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>從 PowerShell 資源庫安裝 SqlServer 模組。

1. 在 Azure 自動化帳戶中，按一下**模組**，然後**瀏覽資源庫**。

2. 在 [搜尋] 列中，搜尋**SqlServer**。

    ![搜尋模組](./media/analysis-services-refresh-azure-automation/1.png)

3. 選取 sql Server，然後按一下 **匯入**。
 
    ![匯入模組](./media/analysis-services-refresh-azure-automation/2.png)

4. 按一下 [確定]  。
 
### <a name="create-a-service-principal-spn"></a>建立服務主體 (SPN)

若要了解如何建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services 中設定權限
 
您建立服務主體必須具有伺服器系統管理員權限，在伺服器上。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

## <a name="design-the-azure-automation-runbook"></a>設計 Azure 自動化 Runbook

1. 在 自動化帳戶中，建立**認證**資源將會用來安全地儲存服務主體。

    ![建立認證](./media/analysis-services-refresh-azure-automation/6.png)

2. 輸入認證的詳細資料。  針對**使用者名**，輸入**SPN ClientId**，如**密碼**，輸入**SPN 祕密**。

    ![建立認證](./media/analysis-services-refresh-azure-automation/7.png)

3. 匯入自動化 Runbook

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. 瀏覽**重新整理 Model.ps1**檔案中，提供**名稱**並**描述**，然後按一下**建立**。

    ![Import Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. 建立 Runbook 之後，它將會自動進入編輯模式。  選取 [發行]。 

    ![發佈 Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 建立認證資源先前擷取 runbook 使用**Get-automationpscredential**命令。  此命令接著會傳遞給**Invoke ProcessASADatabase** PowerShell 命令來執行對 Azure Analysis Services 的驗證。

6. 按一下以測試 runbook**啟動**。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. 填寫**DATABASENAME**， **ANALYSISSERVER**，並**REFRESHTYPE**參數，，然後按一下**確定**。 **WEBHOOKDATA**手動執行 Runbook 時，不需要參數。

    ![啟動 Runbook](./media/analysis-services-refresh-azure-automation/12.png)

如果 Runbook 執行成功，您會收到類似下列的輸出：

![成功執行](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>使用獨立的 Azure 自動化 Runbook

Azure Analysis Services 模型重新整理排程為基礎的觸發程序，就可以設定 Runbook。

這可以如下設定：

1. 在自動化 Runbook 中，按一下**排程**，然後**加入排程**。
 
    ![建立排程](./media/analysis-services-refresh-azure-automation/14.png)

2. 按一下 **排程** > **建立新的排程**，然後填入詳細資料。

    ![設定排程](./media/analysis-services-refresh-azure-automation/15.png)

3. 按一下頁面底部的 [新增]  。

4. 填入排程參數。 這些會在每次觸發 Runbook。 **WEBHOOKDATA**參數應為空白時透過排程執行。

    ![設定參數](./media/analysis-services-refresh-azure-automation/16.png)

5. 按一下 [確定]  。

## <a name="consume-with-data-factory"></a>使用 Data Factory 使用

若要使用 Azure Data Factory，以使用 runbook，先建立**Webhook** runbook。 **Webhook**會提供可以透過 Azure Data Factory web 活動呼叫的 URL。

> [!IMPORTANT]
> 若要建立**Webhook**，Runbook 的狀態必須是**已發佈**。

1. 在自動化 Runbook 中，按一下**Webhook**，然後按一下**新增 Webhook**。

   ![新增 Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. 請給予 Webhook 的名稱和有效期限。  名稱只會識別在自動化 Runbook 的 Webhook，它不會形成 URL 的一部分。

   >[!CAUTION]
   >請確定您在關閉精靈，因為您沒辦法收回一次關閉之前複製的 URL。
    
   ![設定 Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook 的參數可以保留空白。  在設定 Azure Data Factory web 活動時，可以傳入 web 呼叫主體中傳遞的參數。

3. 在 Data Factory 設定**web 活動**

### <a name="example"></a>範例

   ![範例 Web 活動](./media/analysis-services-refresh-azure-automation/19.png)

**URL**是建立 webhook 的 URL。

**主體**是 JSON 文件，其中應包含下列屬性：


|屬性  |值  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 資料庫的名稱 <br/> 範例：AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services 伺服器名稱。 <br/> 範例： https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |若要執行的重新整理的類型。 <br/> 範例：完整         |

範例 JSON 主體：

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Runbook PowerShell 指令碼中定義這些參數。  執行 web 活動時，JSON 承載傳遞是 WEBHOOKDATA。

這是還原序列化並儲存為 PowerShell 參數，然後由叫用 ProcesASDatabase PowerShell 命令。

![已還原序列化的 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>使用 Azure Analysis Services 中的混合式背景工作

Azure 虛擬機器具有靜態公用 IP 位址可用來當做 Azure 自動化混合式背景工作角色。  此公用 IP 位址再加入 Azure Analysis Services 防火牆。

> [!IMPORTANT]
> 請確定虛擬機器的公用 IP 位址會設定為靜態。
>
>若要深入了解設定 Azure 自動化混合式背景工作角色，請參閱[自動化您的資料中心或雲端中的資源使用混合式 Runbook 背景工作角色](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)。

一旦設定混合式背景工作，建立 Webhook 一節中所述[使用 Data Factory 的取用](#consume-with-data-factory)。  唯一的差別是選取**上執行** > **Hybrid Worker**選項時設定 Webhook。

範例 webhook 使用混合式背景工作角色：

![範例混合式背景工作角色 Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell Runbook 範例

下列程式碼片段是如何執行 Azure Analysis Services 模型重新整理使用 PowerShell Runbook 範例。

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>後續步驟

[範例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
