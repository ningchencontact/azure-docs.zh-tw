---
title: "針對常見的 Azure 自動化問題進行疑難排解 | Microsoft Docs"
description: "本文提供相關資訊，以便針對常見 Azure 自動化錯誤進行疑難排解和修正。"
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
tags: top-support-issue
keywords: "自動化錯誤, 疑難排解, 問題"
ms.assetid: 5f3cfe61-70b0-4e9c-b892-d02daaeee07d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: sngun; v-reagie
ms.openlocfilehash: 29362ea94fb86f86f7ff85be81cbf33fef6accce
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>針對 Azure 自動化中的常見問題進行疑難排解 
本文提供針對您在 Azure 自動化中可能遇到的一些常見錯誤進行疑難排解的協助，並且建議可能的解決方法。

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>使用 Azure 自動化 Runbook 時的驗證錯誤
### <a name="scenario-sign-in-to-azure-account-failed"></a>案例：登入 Azure 帳戶失敗
**錯誤：**使用 Add-AzureAccount 或 Login-AzureRmAccount Cmdlet 時，收到錯誤「Unknown_user_type：未知的使用者類型」。

**錯誤的原因：**如果認證資產名稱無效，或使用者名稱和您用來設定自動化認證資產的密碼不正確，就會發生此錯誤。

**疑難排解秘訣：** 為了判斷錯誤原因，請執行下列步驟：  

1. 請確定您沒有任何特殊字元，包括用來連接到 Azure 的「自動化」認證資產名稱中的 **@** 符號。  
2. 請檢查您可以使用儲存在本機 PowerShell ISE 編輯器中的 Azure 自動化認證的使用者名稱和密碼。 您可以藉由在 PowerShell ISE 中執行下列 Cmdlet 來完成這項操作：  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred
3. 如果您的驗證在本機失敗，這表示您尚未正確設定 Azure Active Directory 認證。 請參閱 [Authenticating to Azure using Azure Active Directory (使用 Azure Active Directory 對 Azure 進行驗證)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 部落格文章，以正確設定 Azure Active Directory 帳戶。  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>案例：找不到 Azure 訂用帳戶
**錯誤︰**使用 Select-AzureSubscription 或 Select-AzureRmSubscription Cmdlet 時，收到錯誤訊息「找不到名為 ``<subscription name>`` 的訂用帳戶」。

**錯誤的原因：** 如果訂用帳戶名稱無效，或嘗試取得訂用帳戶詳細資料的 Azure Active Directory 使用者沒有被設定為訂用帳戶的系統管理員，就會發生此錯誤。

**疑難排解秘訣：** 如要判斷您是否已正確地向 Azure 驗證自己的身分，以及您是否有您嘗試選取之訂用帳戶的存取權，請執行下列步驟：  

1. 確認您在執行 **Select-AzureSubscription** Cmdlet 之前，已經執行 **Add-AzureAccount**。  
2. 如果您仍然看到此錯誤訊息，請修改您的程式碼，方法是在 **Add-AzureAccount** Cmdlet 之後新增 **Get-AzureSubscription** Cmdlet，然後執行此程式碼。 現在，請確認 Get-AzureSubscription 的輸出是否包含您的訂用帳戶詳細資料。  

   * 如果您在輸出中未看到任何訂用帳戶詳細資料，這表示訂用帳戶尚未初始化。  
   * 如果您的確在輸出中看到訂用帳戶詳細資料，請利用 **Select-AzureSubscription** Cmdlet 來確認您使用的是正確的訂用帳戶名稱或 ID。   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證
**錯誤：**您會收到錯誤 「 Add-azureaccount: AADSTS50079： 增強式驗證註冊 （證明最多） 是必要 」 使用您的 Azure 使用者名稱和密碼向 Azure 進行驗證時。

**錯誤的原因：** 如果您的 Azure 帳戶使用多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 相反地，您必須使用憑證或服務主體來向 Azure 驗證。

**疑難排解秘訣：**若要使用 Azure 傳統部署模型的指令程式使用的憑證，請參閱[建立並加入要管理 Azure 服務的憑證。](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) 若要搭配 Azure Resource Manager Cmdlet 來使用服務主體，請參閱[使用 Azure 入口網站來建立服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)和[使用 Azure Resource Manager 驗證服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>使用 Runbook 時的常見錯誤
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>案例：已嘗試啟動 Runbook 作業三次，但無法每次都能啟動
**錯誤：**在 runbook 失敗，發生錯誤 「"作業已嘗試三次但失敗。 」

**錯誤原因：**這個錯誤可能是下列原因所造成：  

1. 記憶體限制。 對沙箱配置多少記憶體中有記載的限制[自動化服務限制](../azure-subscription-service-limits.md#automation-limits)因此如果它正在使用的記憶體超過 400 MB，作業可能失敗它。 

2. 模組不相容。 這可能是如果模組相依性不正確，而且不是，如果您的 runbook 通常會傳回"命令找不到 」 或 「 無法繫結參數 」 訊息。 

**疑難排解秘訣：**任何下列解決方案可以修正此問題：

* 在記憶體限制內運作的建議方法是分割多個 Runbook 之間的工作負載、不要處理記憶體中的太多資料、不要寫入來自 Runbook 的不必要輸出，或考慮您寫入 PowerShell 工作流程 Runbook 的檢查點數目。  

* 下列步驟來更新您的 Azure 模組[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)。  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>案例：Runbook 因還原序列化物件而失敗
**錯誤：``<ParameterName>``您的 Runbook 失敗且具有錯誤「無法繫結參數** 。 無法將類型還原序列化 ``<ParameterType>`` 的 ``<ParameterType>`` 值轉換為類型 ``<ParameterType>``」。

**錯誤的原因：** 如果您的 Runbook 是 PowerShell 工作流程，它會以還原序列化格式儲存複雜物件，以在暫止工作流程時保存 Runbook 狀態。

**疑難排解秘訣：**下列三個方案的任何修正此問題：

1. 如果您要將複雜物件從某個 Cmdlet 傳送到另一個 Cmdlet，請將這些 Cmdlet 包裝在 InlineScript 中。
2. 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
3. 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>案例：Runbook 作業失敗，因為超過已配置的配額
**錯誤：** 您的 Runbook 作業失敗且有錯誤「這個訂用帳戶的每月總計作業執行時間的配額已觸達」。

**錯誤的原因：** 當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作，例如，測試作業、從入口網站中開始作業、使用 Webhook 執行作業，使用 Azure 入口網站或您的資料中心排程作業以執行。 若要深入了解自動化定價，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

**疑難排解秘訣：**如果您想要使用的處理每個月 500 分鐘以上，您需要變更您的訂用帳戶從免費層，以 「 基本 」 層。 您可以採取下列步驟，升級至基本層：  

1. 登入您的 Azure 訂用帳戶：  
2. 選取您想要升級的自動化帳戶  
3. 按一下 [設定]  >  [定價]。
4. 按一下頁面底端的 [啟用]，將您的帳戶升級至**基本**層。

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>案例：執行 Runbook 時，無法辨識 Cmdlet
**錯誤：``<cmdlet name>``您的 Runbook 作業失敗，且出現「**：無法辨識 ``<cmdlet name>`` 詞彙是否為 Cmdlet、函式、指令檔或可執行程式的名稱」錯誤。

**錯誤的原因：** 當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 這可能是因為包含 Cmdlet 的模組不在此帳戶、與 Runbook 名稱有名稱衝突，或 Cmdlet 也存在於另一個模組且自動化無法解析名稱。

**疑難排解秘訣：**任何下列解決方案可以修正此問題：  

* 請檢查是否輸入正確的 Cmdlet 名稱。  
* 請確定 Cmdlet 存在於您的自動化帳戶中且沒有衝突。 如要確認 Cmdlet 是否存在，請以編輯模式開啟 Runbook，然後搜尋您想要在程式庫中尋找的 Cmdlet，或執行 **Get-Command ``<CommandName>``**。 一旦驗證 Cmdlet 可用於帳戶，且與其他 Cmdlet 或 Runbook 沒有名稱衝突，將其加入至畫布，並確保您在 Runbook 中使用有效的參數集。  
* 如果有名稱衝突，而且 Cmdlet 可以在兩個不同的模組中使用，您可以使用 Cmdlet 的完整名稱來解決此問題。 例如，您可以使用 **ModuleName\CmdletName**。  
* 如果您是以混合式背景工作角色群組身分在內部部署環境中執行 Runbook，則請確定 module/cmdlet 是安裝在裝載混合式背景工作角色的電腦上。

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>案例：長時間執行的 Runbook 不斷失敗，且伴隨例外狀況：「工作無法繼續執行，因為它不斷在相同的檢查點遭到撤銷」。
**錯誤的原因：**這是故意設計的行為，原因是針對 Azure 自動化中程序的「公平共用」監視，也就是如果 Runbook 的執行時間超過三小時，就會自動暫停。 不過，傳回的錯誤訊息不會提供「接下來該怎麼辦」的選項。 有許多原因能造成 Runbook 暫停， 而大多數是因為發生錯誤。 例如，runbook、 網路失敗，或在執行 runbook 的 Runbook Worker 上損毀的遺漏例外狀況，all 會導致暫止並從其最後一個檢查點繼續時啟動 runbook。

**疑難排解秘訣：** 能避免此問題的已記載解決方案，就是在工作流程中使用檢查點。 若要深入了解，請參閱[了解 PowerShell 工作流程](automation-powershell-workflow.md#checkpoints)。 如需對於「公平共用」及檢查點更詳細的說明，請參閱這篇部落格文章：[在 Runbook 中使用檢查點](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/)。

## <a name="common-errors-when-importing-modules"></a>匯入模組時的常見錯誤
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>案例：無法匯入模組，或無法在匯入之後執行 Cmdlet
**錯誤：** 模組無法匯入，或是匯入成功，但沒有擷取到任何 Cmdlet。

**錯誤的原因：** 可能讓模組無法成功匯入 Azure 自動化的部分常見原因：

* 結構不符合自動化所需要的結構。
* 此模組是相依於尚未部署到您的自動化帳戶的另一個模組。
* 模組在資料夾中遺失其相依性。
* 您使用 **New-AzureRmAutomationModule** Cmdlet 來上傳模組，但您尚未提供完整的儲存體路徑，或是尚未使用可公開存取的 URL 來載入模組。

**疑難排解秘訣：**任何下列解決方案可以修正此問題：

* 請確定此模組會遵循下列格式： ModuleName.Zip  **->** 模組名稱或版本號碼 **->**  （ModuleName.psm1、 ModuleName.psd1）
* 開啟 .psd1 檔案，並且查看該模組是否有任何相依性。 如果有，請將這些模組上傳至自動化帳戶。
* 請確定任何參考的 .dll 會出現在模組資料夾。

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>使用 Desired State Configuration (DSC) 時的常見錯誤
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>案例： 節點處於失敗狀態，並會出現 「 找不到 」 錯誤
**錯誤：**的節點沒有與報表**失敗**狀態，以及含有錯誤"嘗試從伺服器 https:// 取得動作``<url>``//accounts/ ``<account-id>`` /Nodes(AgentId=``<agent-id>``) /GetDscAction 失敗，因為是有效的組態``<guid>``找不到。 」

**錯誤的原因：** 此錯誤通常在您將節點指派給組態名稱 (例如 ABC)，而不是指派給節點組態名稱 (例如 ABC.WebServer) 時發生。

**疑難排解秘訣：**

* 請確定您會使用「節點組態名稱」而不是「組態名稱」來指派節點。
* 您可以使用 Azure 入口網站或使用 PowerShell Cmdlet，將節點組態指派至節點。

  * 如要使用 Azure 入口網站來將節點組態指派給節點，請開啟 [DSC 節點] 分頁、選取某個節點，然後按一下 [指派節點組態] 按鈕。  
  * 如要使用 PowerShell Cmdlet 來將節點組態指派給節點，請使用 **Set-AzureRmAutomationDscNode** Cmdlet

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>案例：編譯組態時沒有產生任何節點組態 (MOF 檔案)
**錯誤：** DSC 編譯工作暫停發生錯誤: 「 編譯成功完成，但所產生的任何節點組態.mofs 」。

**錯誤的原因：**時後面的運算式**節點**DSC 組態中的關鍵字會評估為`$null`，則所不產生的任何節點設定。

**疑難排解秘訣：**任何下列解決方案可以修正此問題：

* 請確定組態定義中「Node」  關鍵字後方的運算式，不會被評估為 $null。
* 如果您在編譯組態時傳遞 ConfigurationData，請確定您傳遞的是組態向 [ConfigurationData](automation-dsc-compile.md#configurationdata)要求的預期值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>案例： DSC 節點報告會變成困在 「 進行中 」 狀態
**錯誤：** DSC 代理程式會輸出 「 沒有執行個體找到具有指定的屬性值。 」

**錯誤的原因：** 您已經將 WMF 版本升級，且擁有損毀的 WMI。

**疑難排解秘訣：**中的指示修正問題後續[已知問題和限制的 DSC](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc)發行項。

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>案例：無法在 DSC 組態中使用認證
**錯誤：** DSC 編譯工作暫止發生錯誤: 「 System.InvalidOperationException 錯誤處理屬性 「 認證 」 類型的``<some resource name>``： 轉換及儲存加密的密碼時，才允許純文字PSDscAllowPlainTextPassword 設為 true 」。

**錯誤的原因：**您已在組態中使用認證，但沒有提供適當的 **ConfigurationData**，以便把每個節點組態的 **PSDscAllowPlainTextPassword** 設定為 True。

**疑難排解秘訣：**

* 請確認傳入適當的 **ConfigurationData**，以便把組態中提到的每個節點組態的 **PSDscAllowPlainTextPassword** 設定為 True。 如需詳細資訊，請參閱[資產在 Azure Automation DSC](automation-dsc-compile.md#assets)。

## <a name="common-errors-when-onboarding-solutions"></a>常見的錯誤時登入解決方案

當登入解決方案可能會遇到錯誤。 以下是您可能會跨執行的常見錯誤的清單。

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**錯誤的原因：**

這個錯誤碼表示未正確格式化儲存的搜尋電腦群組查詢用來鎖定目標方案。 您可能會改變查詢，或它可能已遭修改系統。

**疑難排解秘訣：**

您可以刪除此解決方案，以及 reonboard 方案，就會重新建立查詢的查詢。 查詢可找到您的工作區中位於**已儲存的搜尋**。 查詢的名稱是**MicrosoftDefaultComputerGroup**，而查詢的類別是與這個查詢相關聯的方案名稱。 如果已啟用多個方案， **MicrosoftDefaultComputerGroup**底下顯示多次**已儲存的搜尋**。

## <a name="next-steps"></a>後續步驟

如果您已依照先前的疑難排解步驟，找不到答案，您可以檢閱其他的支援下列選項：

* 取得 Azure 專家協助。 提交您的問題來[MSDN Azure 或堆疊溢位論壇](https://azure.microsoft.com/support/forums/)。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後在 [技術及帳務支援] 下方按一下 [取得支援]。
* 如果您在尋找 Azure 自動化 Runbook 解決方案或整合模組，請在 [指令碼中心](https://azure.microsoft.com/documentation/scripts/) 提出指令碼要求。
* 請在 [User Voice](https://feedback.azure.com/forums/34192--general-feedback)上張貼 Azure 自動化的意見反應或功能要求。
