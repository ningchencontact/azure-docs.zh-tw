---
title: Azure 自動化 Linux 混合式 Runbook 背景工作
description: 本文提供有關安裝 Azure 自動化混合式 Runbook 背景工作角色的資訊，它可讓您在本機資料中心或雲端環境內的 Linux 電腦上執行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aca68b6e8d0e6b80a1504b16b9b3462f20fdc6c4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195653"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>如何部署 Linux 混合式 Runbook 背景工作角色

Azure 自動化的混合式 Runbook 背景工作角色功能可讓您直接在裝載角色的電腦上，以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Linux 混合式 Runbook 背景工作角色可利用特殊使用者身分執行 Runbook，此身分的權限經過提升後可執行需要提高權限的命令。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。 本文會說明如何在 Linux 機器上安裝混合式 Runbook 背景工作角色。

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

以下是支援的 Linux 散發套件清單：

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS (x86/x64)
* SUSE Linux Enteprise Server 11 和 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>安裝 Linux 混合式 Runbook 背景工作角色

若要在 Linux 電腦上安裝和設定混合式 Runbook 背景工作角色，您可遵循直觀的程序手動安裝和設定此角色。 您必須在 Log Analytics 工作區中啟用 [自動化混合式背景工作角色] 解決方案，然後執行一組命令將電腦註冊為背景工作角色，再將它新增至新的或現有的群組。

Linux 混合式 Runbook 背景工作角色的最低需求如下：

* 至少雙核心
* 至少 4 GB 的 RAM
* 連接埠 443 (輸出)

### <a name="package-requirements"></a>封裝需求

| **必要封裝** | **說明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 程式庫| 2.5-12 |
|Openssl| OpenSSL 程式庫 | 0.9.8e 或 1.0|
|Curl | cURL Web 用戶端 | 7.15.5|
|Python-ctypes | |
|PAM | 插入式驗證模組|

繼續之前，您必須記下與自動化帳戶連結的 Log Analytics 工作區，以及自動化帳戶的主索引鍵。 您只要選取您的自動化帳戶，再針對工作區識別碼選取 [工作區]，針對主索引鍵選取 [索引鍵]，即可在入口網站中找到這兩項資訊。 如需混合式 Runbook 背景工作角色所需的連接埠和位址資訊，請參閱[設定網路](automation-hybrid-runbook-worker.md#network-planning)。

1. 在 Azure 中啟用 [自動化混合式背景工作角色] 解決方案。 執行方式可以是下列任一項：

   1. 使用[將 Log Analytics 管理解決方案新增至您的工作區](../log-analytics/log-analytics-add-solutions.md)所述的程序，將**自動化混合式背景工作角色**解決方案新增至您的訂用帳戶。
   1. 執行下列 Cmdlet：

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 執行下列命令並將 \<WorkspaceID\> 和 \<WorkspaceKey\> 取代為工作區的適當值，以安裝適用於 Linux 的 OMS 代理程式。

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 執行下列命令，變更 -w、-k、-g 和 -e 參數的值。 對於 -g 參數，請將其值取代為新的 Linux 混合式 Runbook 背景工作角色應加入之混合式 Runbook 背景工作角色群組的名稱。 如果您的自動化帳戶中還沒有該名稱，則會以該名稱建立新的混合式 Runbook 背景工作角色群組。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 命令完成之後，Azure 入口網站中的 [混合式背景工作角色群組] 頁面會顯示新的群組和成員數目，或者若是現有的群組，則成員數目會遞增。 您可以從 [Hybrid Worker 群組] 頁面上的清單中選取群組，然後選取 [Hybrid Worker] 圖格。 在 [Hybrid Worker] 頁面上，您會看到列出群組的每個成員。

## <a name="turning-off-signature-validation"></a>關閉簽章驗證

根據預設，Linux 混合式 Runbook 背景工作角色需要簽章驗證。 如果您對背景工作角色執行未簽署的 Runbook，就會看到包含「簽章驗證失敗」的錯誤。 若要關閉簽章驗證，請執行下列命令，並以您的 Log Analytics 工作區識別碼取代第二個參數：

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>支援的 Runbook 類型

Linux 混合式 Runbook 背景工作角色並不支援在 自動化中找到的一整組 Runbook 類型。

下列 Runbook 類型可在 Linux 混合式背景工作角色運作：

* Python 2
* PowerShell

下列 Runbook 類型不可在 Linux 混合式背景工作角色運作：

* PowerShell 工作流程
* 圖形化
* 圖形化 PowerShell 工作流程

## <a name="troubleshooting"></a>疑難排解

Linux 混合式 Runbook 背景工作角色仰賴適用於 Linux 的 OMS 代理程式來與自動化帳戶通訊，以便註冊背景工作角色、接收 Runbook 作業和報告狀態。 如果註冊背景工作角色失敗，請參考以下一些可能的錯誤原因：

### <a name="the-oms-agent-for-linux-is-not-running"></a>適用於 Linux 的 OMS 代理程式未執行

如果適用於 Linux 的 OMS 代理程式未執行，就會讓 Linux 混合式 Runbook 背景工作角色無法與 Azure 自動化進行通訊。 輸入下列命令，確認代理程式正在執行：`ps -ef | grep python`。 您所看到的輸出應該會類似下列具有 **nxautomation** 使用者帳戶的 Python 處理序。 如果未啟用 [更新管理] 或 [Azure 自動化] 解決方案，下列處理序都不會執行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

下列清單顯示已針對 Linux 混合式 Runbook 背景工作角色啟動的處理序。 這些處理序全都位於 `/var/opt/microsoft/omsagent/state/automationworker/` 目錄。

* **oms.conf** - 這是背景工作角色管理員處理序，會直接從 DSC 啟動。

* **worker.conf** - 此處理序是自動註冊的混合式背景工作角色處理序，會由背景工作角色管理員來啟動。 更新管理會使用此處理序，且使用者不會注意到此處理序。 如果機器上未啟用更新管理解決方案，就不會出現此處理序。

* **diy/worker.conf** - 此處理序是 DIY 混合式背景工作處理序。 DIY 混合式背景工作處理序可用來在混合式 Runbook 背景工作角色上執行使用者 Runbook。 它與自動註冊的混合式背景工作處理序的主要差別在於會使用不同的設定。 如果未啟用 Azure 自動化解決方案，而且未註冊 DIY Linux 混合式背景工作角色，就不會出現此處理序。

如果適用於 Linux 的 OMS 代理程式未執行，請執行下列命令來啟動服務：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="the-specified-class-does-not-exist"></a>指定的類別不存在

如果您看到**指定類別不存在...** 的錯誤訊息出現在  `/var/opt/microsoft/omsconfig/omsconfig.log` 中，表示適用於 Linux 的 OMS 代理程式需要更新。 請執行下列命令來重新安裝 OMS 代理程式：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

如需如何針對更新管理的問題進行疑難排解的其他步驟，請參閱[更新管理 - 疑難排解](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>後續步驟

* 請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。
* 如需移除混合式 Runbook 背景工作角色的指示，請參閱[移除 Azure 自動化混合式 Runbook 背景工作角色](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
