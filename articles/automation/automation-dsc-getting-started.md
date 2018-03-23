---
title: 開始使用 Azure 自動化 DSC
description: Azure 自動化預期狀態設定 (DSC) 中最常見工作的說明和範例
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e6e4df6fce783e7b4cc33d7f3c26136680ceb44f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="getting-started-with-azure-automation-dsc"></a>開始使用 Azure 自動化 DSC
本文說明如何使用 Azure 自動化預期狀態設定 (DSC) 來執行最常見的工作，例如建立、匯入和編譯組態、將要管理的機器上線，以及檢視報告。 如需何謂 Azure 自動化 DSC 的概觀，請參閱 [Azure 自動化 DSC 概觀](automation-dsc-overview.md)。 如需 DSC 文件，請參閱 [Windows PowerShell 預期狀態設定概觀](https://msdn.microsoft.com/PowerShell/dsc/overview)。

本文提供使用 Azure 自動化 DSC 的逐步指南。 如果您想要一個未遵循本文所述之步驟所設定的範例環境，您可以使用下列 Resource Manager 範本：此範本會設定完整的 Azure Automation DSC 環境，包括受到 Azure Automation DSC 管理的 Azure VM。

## <a name="prerequisites"></a>先決條件
若要完成本文中的範例，需要有下列項目：

* Azure 自動化帳戶。 如需建立 Azure 自動化執行身分帳戶的指示，請參閱 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)。
* 執行 Windows Server 2008 R2 或更新版本的 Azure Resource Manager VM (不是傳統)。 如需建立 VM 的指示，請參閱 [在 Azure 入口網站中建立第一個 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>建立 DSC 組態
您會建立一個簡單的 [DSC 設定](https://msdn.microsoft.com/powershell/dsc/configurations)，以根據您指派節點的方式，確定是否有 **Web 伺服器** Windows 功能 (IIS) 存在。

1. 啟動 Windows PowerShell ISE (或任何文字編輯器)。
2. 輸入下列文字：
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
   
            }
        }
   
        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
   
            }
        }
        }
    ```
3. 將檔案儲存為 `TestConfig.ps1`。

此設定會在每個節點區塊中呼叫一個資源 ( [WindowsFeature 資源](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource))，這可確保 **Web 伺服器** 功能是否存在。

## <a name="importing-a-configuration-into-azure-automation"></a>將組態匯入 Azure 自動化
接下來，您會將設定匯入自動化帳戶。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，選取 [組態管理] 下方的 [DSC 設定]。
4. 在 [DSC 設定] 頁面上，按一下 [+ 加入設定]。
5. 在 [匯入設定] 頁面上，瀏覽至您電腦上的 `TestConfig.ps1` 檔案。
   
    ![[匯入組態] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/AddConfig.png)
6. 按一下 [SERVICEPRINCIPAL] 。

## <a name="viewing-a-configuration-in-azure-automation"></a>在 Azure 自動化中檢視組態
在匯入組態之後，您可以在 Azure 入口網站中檢視它。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，選取 [組態管理] 下方的 [DSC 設定]。
4. 在 [DSC 設定] 頁面上，按一下 [TestConfig] (這是您在上一個程序中匯入的設定名稱)。
5. 在 [TestConfig 設定] 頁面上，按一下 [檢視設定來源]。
   
    ![TestConfig 組態刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    [TestConfig 設定來源]  刀鋒視窗隨即開啟，顯示此設定的 PowerShell 程式碼。

## <a name="compiling-a-configuration-in-azure-automation"></a>在 Azure 自動化中編譯組態
定義預期狀態的 DSC 組態必須先編譯成一或多個節點組態 (MOF 文件)，並放在自動化 DSC 提取伺服器上，才可以將該預期狀態套用至節點。 如需在 Azure 自動化 DSC 中編譯設定的詳細說明，請參閱 [在 Azure 自動化 DSC 中編譯設定](automation-dsc-compile.md)。 如需編譯設定的詳細資訊，請參閱 [DSC 設定](https://msdn.microsoft.com/PowerShell/DSC/configurations)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [組態管理] 下方的 [DSC 設定]。
4. 在 [DSC 設定] 頁面上，按一下 [TestConfig] (先前匯入的設定名稱)。
5. 在 [TestConfig 設定] 頁面上，按一下 [編譯]，然後按一下 [是]。 這會啟動編譯作業。
   
    ![醒目提示 [編譯] 按鈕之 TestConfig 設定頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> 當您在 Azure 自動化中編譯組態時，它會自動將任何已建立的節點組態 MOF 部署至提取伺服器。
> 
> 

## <a name="viewing-a-compilation-job"></a>檢視編譯作業
啟動編譯之後，您可以在 [組態] 刀鋒視窗的 [編譯作業] 圖格中檢視它。 [編譯作業]  圖格會顯示目前執行中、已完成及失敗的工作。 當您開啟編譯作業刀鋒視窗時，它會顯示該工作的相關資訊，包括發生的任何錯誤或警告、組態中使用的參數以及編譯記錄檔。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [組態管理] 下方的 [DSC 設定]。
4. 在 [DSC 設定] 頁面上，按一下 [TestConfig] (先前匯入的設定名稱)。
5. 在 [編譯工作] 下方，選取您想要檢視的編譯工作。 [編譯工作] 頁面隨即開啟，並標示編譯工作的啟動日期。
   
    ![[編譯工作] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/CompilationJob.png)
6. 按一下 [編譯工作] 頁面中的任何磚，以查看工作的進一步詳細資料。

## <a name="viewing-node-configurations"></a>檢視節點組態
成功完成編譯作業會建立一或多個新的節點組態。 節點組態是已部署到提取伺服器且準備由一或多個節點提取並套用的 MOF 文件。 您可以在 [DSC 節點設定]  刀鋒視窗中檢視您的自動化帳戶的節點設定。 節點組態的名稱格式為 ConfigurationName.NodeName。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，依序按一下 [所有資源]  和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 刀鋒視窗上，按一下 [DSC 節點組態]。
   
    ![[DSC 節點組態] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>將 Azure VM 上架以供 Azure 自動化 DSC 管理
您可以使用 Azure 自動化 DSC 來管理 Azure VM (傳統和 Resource Manager)、內部部署 VM、Linux 機器、AWS VM 和內部部署實體機器。 在本文中，您將了解如何只將 Azure Resource Manager VM 上線。 如需將其他類型的機器上架的詳細資訊，請參閱 [將機器上架以供 Azure 自動化 DSC 管理](automation-dsc-onboarding.md)。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>將 Azure Resource Manager VM 上架以便 Azure 自動化 DSC 管理
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 分頁上，按一下 [組態管理] 下方的 [DSC 節點]。
4. 在 [DSC 節點] 頁面中，按一下 [加入 Azure VM]。
   
    ![醒目提示 [加入 Azure VM] 按鈕之 [DSC 節點] 頁面的螢幕擷取畫面](./media/automation-dsc-getting-started/OnboardVM.png)
5. 在 [虛擬機器] 分頁上，選取您的 VM。 在 [加入 Azure VM] 頁面中，按一下 [選取要上線的虛擬機器]。
6. 按一下 [ **連接**]。
   
   > [!IMPORTANT]
   > 這必須是執行 Windows Server 2008 R2 或更新版本的 Azure Resource Manager VM。
   > 
   > 
1. 在 [註冊] 頁面的 [節點設定名稱] 方塊中，輸入您要套用至 VM 的節點設定名稱。 這必須完全符合自動化帳戶中的節點組態名稱。 在此時提供名稱是選擇性的。 您可以在節點上架後，變更指派的節點組態。
   勾選 [必要時重新啟動節點]，然後按一下 [確定]。
   
    ![[註冊] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/RegisterVM.png)
   
    您指定的節點設定會依 [設定模式頻率] 所指定的間隔套用到 VM，而 VM 會依 [重新整理頻率] 所指定的間隔檢查節點設定的更新。 如需有關如何使用這些值的詳細資訊，請參閱 [設定本機設定管理員](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)。
9. 在 [新增 Azure VM] 刀鋒視窗中，按一下 [建立]。

Azure 會啟動 VM 上線的程序。 完成時，VM 會顯示在自動化帳戶的 [DSC 節點] 刀鋒視窗中。

## <a name="viewing-the-list-of-dsc-nodes"></a>檢視 DSC 節點的清單
您可以在 [DSC 節點]  刀鋒視窗中檢視您的自動化帳戶中已上架以供管理的所有機器清單。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [DSC 節點]。

## <a name="viewing-reports-for-dsc-nodes"></a>檢視 DSC 節點的報告
每次 Azure 自動化 DSC 在受控節點上執行一致性檢查時，此節點會將狀態報告傳回到提取伺服器。 您可以在該節點的頁面上檢視這些報告。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [DSC 節點]。
4. 在 [DSC 節點] 清單中，選取您想要檢視的節點。
5. 在 [節點] 頁面上，按一下您要在 [報告] 下方檢視的報告。
   
    ![[報告] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/NodeReport.png)

在個別報告的刀鋒視窗上，您可以看到相對應一致性檢查的下列狀態資訊︰

* 報告狀態 — 節點是否「相容」、設定「失敗」，或節點「不相容」(當節點處於 **applyandmonitor** 模式且機器不在預期狀態時)。
* 一致性檢查的開始時間。
* 一致性檢查的總執行時間。
* 一致性檢查的類型。
* 任何錯誤，包括錯誤碼和錯誤訊息。 
* 設定中使用的任何 DSC 資源，以及每個資源的狀態 (不論節點是否處於該資源的預期狀態) — 您可以按一下每個資源，以取得該資源的詳細資訊。
* 節點的名稱、IP 位址和組態模式。

您也可以按一下 [檢視原始報告]  ，查看節點傳送至伺服器的實際資料。 如需有關使用該資料的詳細資訊，請參閱 [使用 DSC 報告伺服器](https://msdn.microsoft.com/powershell/dsc/reportserver)。

在節點上架後，可能需要一些時間才可取得第一份報告。 在節點上架後，您可能必須等待多達 30 分鐘的時間才可取得第一份報告。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>將節點重新指派至不同的節點組態
您可以指派節點以使用不同於您最初指派的節點組態。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [DSC 節點]。
4. 在 [DSC 節點] 頁面上，按一下您要重新指派的節點名稱。
5. 在該節點的頁面上，按一下 [指派節點]。
   
    ![醒目提示 [指派節點] 按鈕之 [節點] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/AssignNode.png)
6. 在 [指派節點設定] 頁面上，選取您要指派節點的節點設定，然後按一下 [確定]。
   
    ![[指派節點組態] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>取消註冊節點
如果您不想再由 Azure 自動化 DSC 管理節點，您可以將該節點取消註冊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序按一下左側的 [所有資源] 和您的自動化帳戶名稱。
3. 在 [自動化帳戶] 頁面上，按一下 [DSC 節點]。
4. 在 [DSC 節點] 頁面上，按一下您要取消註冊的節點名稱。
5. 在該節點的頁面上，按一下 [取消註冊]。
   
    ![醒目提示 [取消註冊] 按鈕之 [節點] 刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>相關文章
* [Azure 自動化 DSC 概觀](automation-dsc-overview.md)
* [上架由 Azure 自動化 DSC 管理的機器](automation-dsc-onboarding.md)
* [Windows PowerShell 預期狀態設定概觀](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure 自動化 DSC Cmdlet](/powershell/module/azurerm.automation/#automation)
* [Azure 自動化 DSC 價格](https://azure.microsoft.com/pricing/details/automation/)

