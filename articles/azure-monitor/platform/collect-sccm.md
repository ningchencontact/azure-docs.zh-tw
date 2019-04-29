---
title: Configuration Manager 連線至 Azure 監視器 |Microsoft Docs
description: 本文介绍将 Configuration Manager 连接到 Azure Monitor 中的工作区以及开始分析数据的步骤。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741440"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>将 Configuration Manager 连接到 Azure Monitor
可将 System Center Configuration Manager 环境连接到 Azure Monitor 以同步设备集合数据，并在 Azure Monitor 和 Azure 自动化中引用这些集合。  

## <a name="prerequisites"></a>必要條件

Azure Monitor 支持 System Center Configuration Manager 当前分支版本 1606 和更高版本。  

## <a name="configuration-overview"></a>組態概觀
以下步骤总结了使用 Azure Monitor 配置 Configuration Manager 的步骤。  

1. 在 Azure 入口網站中，將 Configuration Manager 註冊為 Web 應用程式和/或 Web API 應用程式，並確保您擁有 Azure Active Directory 註冊中的用戶端識別碼和用戶端秘密金鑰。 如需如何完成此步驟的詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)。
2. 在 Azure 门户中，[授予 Configuration Manager（已注册的 Web 应用）访问 Azure Monitor 的权限](#grant-configuration-manager-with-permissions-to-log-analytics)。
3. 在 Configuration Manager 中，使用「新增 OMS 連線精靈」新增連線。
4. 如果密碼或用戶端祕密金鑰過期或遺失，請在 Configuration Manager 中更新連線屬性。
5. 在執行 Configuration Manager 服務連線點網站系統角色的電腦上[下載並安裝 Microsoft Monitoring Agent](#download-and-install-the-agent)。 该代理将 Configuration Manager 数据发送到 Azure Monitor 中的 Log Analytics 工作区。
6. 在 Azure Monitor 中，以计算机组的形式[从 Configuration Manager 导入集合](#import-collections)。
7. 在 Azure Monitor 中，以[计算机组](computer-groups.md)的形式从 Configuration Manager 查看数据。

您可以深入了解 Configuration Manager 連線至在 Azure 監視器[同步處理至 Microsoft Log Analytics 工作區的資料從 Configuration Manager](https://technet.microsoft.com/library/mt757374.aspx)。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>對 Configuration Manager 授與 Log Analytics 的權限
在下列程序中，您會將 Log Analytics 工作區中的「參與者」角色授與給您稍早為 Configuration Manager 建立的 AD 應用程式和服務主體。  如果尚未创建工作区，请参阅[在 Azure Monitor 中创建工作区](../../azure-monitor/learn/quick-create-workspace.md)，然后继续。  這可讓 Configuration Manager 進行驗證並連線到 Log Analytics 工作區。  

> [!NOTE]
> 必须为 Configuration Manager 指定 Log Analytics 工作区中的权限。 否則，當您在 Configuration Manager 中使用組態精靈時，將會收到錯誤訊息。
>

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics]。
2. 在 Log Analytics 工作區清單中，選取要修改的工作區。
3. 從左窗格中，選取 [存取控制 (IAM)]。
4. 在 [存取控制 (IAM)] 頁面上，按一下 [新增角色指派]，[新增角色指派] 窗格隨即出現。
5. 在 [新增角色指派] 窗格中，於 [角色] 下拉式清單中選取 [參與者] 角色。  
6. 在 [指派存取權的對象] 下拉式清單底下，選取稍早在 AD 中建立的 Configuration Manager 應用程式，然後按一下 [確定]。  

## <a name="download-and-install-the-agent"></a>下載並安裝代理程式
檢閱文件[連線的 Windows 電腦連線到 Azure 中的 Azure 監視器](agent-windows.md)若要了解可用來裝載 Configuration Manager 服務連接點的電腦上安裝 Microsoft Monitoring Agent 的方法站台系統角色。  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>新增 Log Analytics 連線至 Configuration Manager
若要新增 Log Analytics 連線，Configuration Manager 環境的[服務連線點](https://technet.microsoft.com/library/mt627781.aspx)必須設定成線上模式。

1. 在 Configuration Manager 的 [管理] 工作區中選取 [OMS 連接器]。 這會開啟 [新增 Log Analytics 連線精靈]。 選取 [下一步] 。

   >[!NOTE]
   >OMS 现在称为 Log Analytics，是 Azure Monitor 的一项功能。
   
2. 在 [一般] 畫面上，確認您已完成下列動作而且您有每個項目的詳細資料，然後選取 [下一步]。

   1. 在 Azure 入口網站中，您已將 Configuration Manager 註冊為 Web 應用程式和/或 Web API 應用程式，而且您擁有[註冊中的用戶端識別碼](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md)。
   2. 在 Azure 入口網站中，您已經為 Azure Active Directory 中已註冊的應用程式建立應用程式祕密金鑰。  
   3. 在 Azure 门户中，已为注册的 Web 应用提供访问 Azure Monitor 中 Log Analytics 工作区的权限。  
      ![連線至 Log Analytics 連線精靈一般頁面](./media/collect-sccm/sccm-console-general01.png)
3. 在“Azure Active Directory”屏幕上，通过提供“租户”、“客户端 ID”，以及“客户端机密密钥”来配置到 Log Analytics 工作区的连接设置，并选择“下一步”。  
   ![連線至 Log Analytics 連線精靈 Azure Active Directory 頁面](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. 如果您成功完成其他所有程序，則 [OMS 連線組態] 畫面上的資訊會自動出現在此頁面。 [Azure 訂用帳戶]、[Azure 資源群組] 和 [Operations Management Suite 工作區] 應該會出現連線設定資訊。  
   ![連線至 Log Analytics 連線精靈 Log Analytics 連線頁面](./media/collect-sccm/sccm-wizard-configure04.png)
5. 此向导将使用已输入的信息连接到 Log Analytics 工作区。 選取想要與 Log Analytics 同步處理的裝置集合，然後按一下 [新增]。  
   ![選取集合](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. 確認 [摘要] 畫面上的連線設定，然後選取 [下一步]。 [進度] 畫面會顯示連線狀態，然後應該 [完成]。

> [!NOTE]
> 必须将层次结构中的顶层站点连接到 Azure Monitor。 如果先将独立主站点连接到 Azure Monitor，再将管理中心站点添加到你的环境，则必须在新层次结构中删除并重新创建连接。
>
>

将 Configuration Manager 链接到 Azure Monitor 后，可以添加或删除集合，并查看连接的属性。

## <a name="update-log-analytics-workspace-connection-properties"></a>更新 Log Analytics 工作区连接属性
如果密碼或用戶端秘密金鑰過期或遺失，您必須手動更新 Log Analytics 連線屬性。

1. 在 Configuration Manager 中瀏覽至 [雲端服務]，然後選取 [OMS 連接器] 以開啟 [OMS 連線屬性] 頁面。
2. 在此頁面上按一下 [Azure Active Directory] 索引標籤，檢視 [租用戶]、[用戶端識別碼] 和 [用戶端秘密金鑰到期]。 **確認** **用戶端秘密金鑰**是否已過期。

## <a name="import-collections"></a>匯入集合
将 Log Analytics 连接添加到 Configuration Manager 并在运行 Configuration Manager 服务连接点站点系统角色的计算机上安装代理之后，下一步是将集合以计算机组的形式从配置服务器导入 Azure Monitor 中。

在完成從階層匯入裝置集合的初始設定後，每 3 個小時就會擷取一次集合成員資格資訊，以便成員資格會隨時保持最新狀態。 您可以隨時選擇停用此功能。

1. 在 Azure 入口網站中，按一下左上角的 [所有服務]。 在資源清單中輸入 **Log Analytics**。 當您開始輸入時，清單會根據您輸入的文字進行篩選。 選取 [Log Analytics 工作區]。
2. 在您的 Log Analytics 工作區清單中，選取 Configuration Manager 用來註冊的工作區。  
3. 選取 [進階設定]。
4. 依序選取 [電腦群組] 和 [SCCM]。  
5. 選取 [匯入 Configuration Manager 集合成員資格]，然後按一下 [儲存]。  
   ![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>從 Configuration Manager 檢視資料
将 Log Analytics 连接添加到 Configuration Manager 并在运行 Configuration Manager 服务连接点站点系统角色的计算机上安装了代理之后，来自代理的数据将发送到 Azure Monitor 中的 Log Analytics 工作区。 在 Azure Monitor 中，Configuration Manager 集合以[计算机组](../../azure-monitor/platform/computer-groups.md)的形式显示。 您可以從 [設定\電腦群組] 下的 [Configuration Manager] 頁面檢視群組。

匯入集合之後，您可以看到已偵測到多少部具有集合成員資格的電腦。 您也可以看到已匯入的集合數目。

![電腦群組 - SCCM 索引標籤](./media/collect-sccm/sccm-computer-groups02.png)

當您按一下其中一項時，[搜尋] 會開啟，顯示所有匯入的群組或是屬於每個群組的所有電腦。 使用 [記錄搜尋](../../azure-monitor/log-query/log-query-overview.md)，您就可以開始深入分析 Configuration Manager 資料。

## <a name="next-steps"></a>後續步驟
* 請使用 [記錄檔搜尋](../../azure-monitor/log-query/log-query-overview.md)，檢視有關 Configuration Manager 資料的詳細資訊。
