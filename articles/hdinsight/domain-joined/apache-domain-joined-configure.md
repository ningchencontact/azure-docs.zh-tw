---
title: "設定已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解如何安裝及設定已加入網域的 HDInsight 叢集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/15/2018
ms.author: saurinsh
ms.openlocfilehash: b4d71eeb0aab75e67e851f867f194ed7578d0d1c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>設定已加入網域的 HDInsight 沙箱環境

了解如何使用獨立的 Active Directory 和 [Apache Ranger](http://hortonworks.com/apache/ranger/) 設定 Azure HDInsight 叢集，以利用增強式驗證和豐富的角色型存取控制 (RBAC) 原則。 如需詳細資訊，請參閱[已加入網域的 HDInsight 叢集簡介](apache-domain-joined-introduction.md)。 

> [!IMPORTANT]
> 根據預設，只能搭配 Azure 儲存體帳戶使用此設定。 若要搭配使用 Azure Data Lake Store，請將 Active Directory 同步處理至新的 Azure Active Directory。

若未使用已加入網域的 HDInsight 叢集，每個叢集只能有一個 Hadoop HTTP 使用者帳戶，和一個 SSH 使用者帳戶。  若需要多使用者驗證，可透過下列方式：

-   在 Azure IaaS 上執行的獨立 Active Directory。
-   Azure Active Directory。

本文會介紹如何使用在 Azure IaaS 上執行的獨立 Active Directory。 對客戶而言，若要取得 HDInsight 的多使用者支援，這是最容易採用的架構。 本文涵蓋兩種設定方式：

- 選項 1：使用 Azure 資源管理範本，建立獨立的 Active Directory 與 HDInsight 叢集。
- 選項 2：整個流程分成下列步驟：
    - 使用範本建立 Active Directory。
    - 設定 LDAPS。
    - 建立 AD 使用者和群組
    - 建立 HDInsight 叢集

> [!IMPORTANT]
> 
> Oozie 未在已加入網域的 HDInsight 上啟用。

## <a name="prerequisite"></a>必要條件
* Azure 訂閱

## <a name="option-1-one-step-approach"></a>選項 1：單一步驟方法
在本節中，請從 Azure 入口網站開啟 Azure 資源管理範本。 此範本是用來建立獨立的 Active Directory 與 HDInsight 叢集。 目前可以建立已加入網域的 Hadoop 叢集、Spark 叢集及互動式查詢叢集。

1. 按一下以下影像，在 Azure 入口網站中開啟範本。 範本位在 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)。
   
    建立 Spark 叢集：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    建立互動式查詢叢集：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    建立 Hadoop 叢集：

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 輸入值，選取 [我同意上方所述的條款及條件]，選取 [釘選到儀表板]，然後按一下 [購買]。 將滑鼠游標暫留在欄位旁邊的說明符號上，即可查看說明。 已填入大部分的值。 您可以使用預設值或您自己的值。

    - **資源群組**：輸入 Azure 資源群組名稱。
    - **位置**：選擇靠近您的位置。
    - **新的儲存體帳戶名稱**：輸入 Azure 儲存體帳戶名稱。 PDC、BDC、HDInsight 叢集會將這個新的儲存體帳戶作為預設儲存體帳戶。
    - **管理員使用者名稱**：輸入網域管理員使用者名稱。
    - **管理員密碼**：輸入網域管理員密碼。
    - **網域名稱**：預設名稱是 *contoso.com*。如果您變更網域名稱，也必須更新 [安全 LDAP 的憑證] 欄位和 [組織單位 DN] 欄位。
    - **DNS 首碼**：輸入 Load Balancer 所使用之公用 IP 位址的 DNS 首碼。
    - **叢集名稱**︰輸入 HDInsight 叢集名稱。
    - **叢集類型**：請勿變更此值。 如果您想要變更叢集類型，請使用最後一個步驟中的特定範本。
    - **安全 Ldap 憑證密碼**：除非您變更 [安全 LDAP] 憑證欄位，否則請使用預設值。
    範本中的某些值是硬式編碼，例如，背景工作角色節點執行個體計數是兩個。  若要變更硬式編碼值，請按一下 [編輯範本]。

    ![已加入網域的 HDInsight 叢集編輯範本](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

範本順利完成之後，資源群組中已建立了 23 個資源。

## <a name="option-2-multi-step-approach"></a>選項 2：多步驟方法

本節有四個步驟︰

1. 使用範本建立 Active Directory。
2. 設定 LDAPS。
3. 建立 AD 使用者和群組
4. 建立 HDInsight 叢集

### <a name="create-an-active-directory"></a>建立 Active Directory

使用 Azure Resource Manager 範本可更輕鬆地建立 Azure 資源。 在本節中，會使用 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/)來建立具有兩部虛擬機器的新樹系和網域。 兩部虛擬機器會作為主要網域控制站和備份網域控制站使用。

**建立具有兩個網域控制站的網域**

1. 按一下以下影像，在 Azure 入口網站中開啟範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    範本外觀如下：

    ![已加入網域的 HDInsight 會建立樹系網域虛擬機器](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. 輸入下列值：

    - **訂用帳戶**：選取 Azure 訂用帳戶。
    - **資源群組名稱**：輸入資源群組名稱。  資源群組用於管理與專案相關的 Azure 資源。
    - **位置**：選取靠近您的 Azure 位置。
    - **管理員使用者名稱**：網域管理員使用者名稱。 此使用者並非您 HDInsight 叢集的 HTTP 使用者帳戶， 而是您在整個教學課程中使用的帳戶。
    - **管理員密碼**：輸入網域管理員的密碼。
    - **網域名稱**：網域名稱必須是兩段式名稱。 例如：contoso.com、contoso.local 或 hdinsight.test。
    - **DNS 首碼**：輸入 DNS 首碼
    - **PDC RDP 連接埠**：(使用本教學課程的預設值)
    - **BDC RDP 連接埠**：(使用本教學課程的預設值)
    - **構件位置**：(使用本教學課程的預設值)
    - **構件位置 SAS 權杖**：(本教學課程中留白)

大約需要 20 分鐘的時間來建立資源。

### <a name="setup-ldaps"></a>設定 LDAPS

輕量型目錄存取通訊協定 (LDAP) 是用來從 AD 讀取和寫入 AD。

**使用遠端桌面連線到 PDC**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟資源群組，然後再開啟主要網域控制站 (PDC) 虛擬機器。 預設 PDC 名稱為 adPDC。 
3. 按一下 [連線]，以使用遠端桌面連線到 PDC。

    ![已加入網域的 HDInsight 會連線 PDC 遠端桌面](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**新增 Active Directory 憑證服務**

4. 開啟 [伺服器管理員] (如果尚未開啟)。
5. 按一下 [管理]，然後按一下 [新增角色及功能]。

    ![已加入網域的 HDInsight 會新增角色與功能](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. 在 [開始之前]，按一下 [下一步]。
6. 選取 [角色型或功能型安裝]，然後按一下 [下一步]。
7. 選取 PDC，然後按一下 [下一步]。  預設 PDC 名稱為 adPDC。
8. 選取 [Active Directory 憑證服務]。
9. 在快顯對話方塊中按一下 [新增功能]。
10. 遵循精靈的指示，在其餘程序使用預設設定。
11. 按一下 [關閉]  即可關閉精靈。

**設定 AD 憑證**

1. 在 [伺服器管理員] 中，按一下黃色通知圖示，然後按一下 [設定 Active Directory 憑證服務]。

    ![已加入網域的 HDInsight 會設定 AD 憑證](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. 按一下左側的 [角色服務]，選取 [憑證授權單位]，然後按一下 [下一步]。
3. 遵循精靈的指示，在其餘程序使用預設設定 (最後一個步驟時，按一下 [設定])。
4. 按一下 [關閉]  即可關閉精靈。

### <a name="optional-create-ad-users-and-groups"></a>(選擇性) 建立 AD 使用者和群組

**於 AD 建立使用者和群組**
1. 使用遠端桌面連線到 PDC
1. 開啟 [Active Directory 使用者及電腦]。
2. 在左窗格中選取您的網域名稱。
3. 在上方功能表中按一下 [在目前的容器中建立一個新使用者] 圖示。

    ![已加入網域的 HDInsight 會建立使用者](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. 依照指示建立一些使用者。 例如，hiveuser1 和 hiveuser2。
5. 在上方功能表中按一下 [在目前的容器中建立一個新群組] 圖示。
6. 依照指示建立名稱為 **HDInsightUsers** 的群組。  稍後在本教學課程中建立 HDInsight 叢集時，會使用到這個群組。

> [!IMPORTANT]
> 建立加入網域的 HDInsight 叢集之前，必須重新啟動 PDC 虛擬機器。

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>在 VNet 中建立 HDInsight 叢集

在本節中，您可以使用 Azure 入口網站，將 HDInsight 叢集新增到稍早在本教學課程中使用 Resource Manager 範本建立的虛擬網路。 本文涵蓋範圍僅限於已加入網域之叢集的特定設定資訊。  如需一般資訊，請參閱[使用 Azure 入口網站在 HDInsight 中建立以 Linux 為基礎的叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)。  

**建立已加入網域的 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟您稍早在本教學課程中使用 Resource Manager 範本建立的資源群組。
3. 將 HDInsight 叢集新增至該資源群組。
4. 選取 [自訂] 選項：

    ![已加入網域的 HDInsight 自訂建立選項](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    有六個區段使用自訂設定選項：基本、儲存體、應用程式、叢集大小、進階設定、摘要。
5. [基本] 區段：

    - 叢集類型：選取 [企業安全性封裝]。 目前只有下列叢集類型可啟用企業安全性封裝：Hadoop、互動式查詢、Spark。

        ![已加入網域的 HDInsight 企業安全性封裝](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - 叢集登入使用者名稱︰Hadoop HTTP 使用者。 此帳戶與網域管理員帳戶不同。
    - 資源群組：選取您稍早使用 Resource Manager 範本建立的資源群組。
    - 位置：位置必須與您使用 Resource Manager 範本建立 vnet 和 DC 時所使用的位置相同。

6. [進階設定] 區段：

    - 網域設定：

        ![已加入網域的 HDInsight 進階設定網域](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - 網域名稱：輸入您在[建立 Active Directory](#create-an-active-directory) 時所用的網域名稱。
        - 網域使用者名稱：輸入您在[建立 Active Directory](#create-an-active-directory) 時所用的 AD 管理員使用者名稱。
        - 組織單位：範例請見螢幕擷取畫面。
        - LDAPS URL：範例請見螢幕擷取畫面
        - 存取使用者群組：輸入您在[建立 AD 使用者和群組](#optionally-createad-users-and-groups)時所建立的使用者群組名稱。
    - 虛擬網路：選取您在[建立 Active Directory](#create-an-active-directory) 時所建立的虛擬網路。 範本中使用的預設名稱為 **adVNET**。
    - 子網路：範本中使用的預設名稱為 **adSubnet**。



完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。 如需有關刪除叢集的指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](../hdinsight-administer-use-management-portal.md#delete-clusters)。

## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

