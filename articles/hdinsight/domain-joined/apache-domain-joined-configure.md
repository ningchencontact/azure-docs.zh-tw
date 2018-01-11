---
title: "設定已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解如何安裝及設定已加入網域的 HDInsight 叢集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/15/2017
ms.author: saurinsh
ms.openlocfilehash: 0a9ed1cad8b8d4c566a0da16ac78d096efe187a5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2017
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>設定網域的 HDInsight 沙箱環境

了解如何設定獨立 Active Directory 與 Azure HDInsight 叢集和[Apache Ranger](http://hortonworks.com/apache/ranger/)利用增強式驗證及豐富的角色型存取控制 (RBAC) 原則。 如需詳細資訊，請參閱[已加入網域的 HDInsight 叢集簡介](apache-domain-joined-introduction.md)。

未加入網域的 HDInsight 叢集，每個叢集只能有 HTTP Hadoop 使用者帳戶，而 SSH 使用者帳戶。  多使用者驗證，可以使用來達成：

-   Active Directory 的獨立 Azure IaaS 上執行
-   Azure Active Directory
-   客戶在內部部署環境上執行的 active Directory。

使用獨立的 Active Directory 在 Azure IaaS 上執行涵蓋在本文中。 它是最簡單的架構，客戶可以遵循以取得 HDInsight 上的多重使用者的支援。 

> [!IMPORTANT]
> Oozie 未在已加入網域的 HDInsight 上啟用。

## <a name="prerequisite"></a>必要條件
* Azure 訂閱

## <a name="create-an-active-directory"></a>建立 Active Directory

Azure 資源管理員範本可讓您更容易建立 Azure 資源。 在本節中，您會使用[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/)兩部虛擬機器以建立新的樹系和網域。 兩部虛擬機器做為主要網域控制站和備份網域控制站。

**若要建立具有兩個網域控制站的網域**

1. 按一下以下影像，在 Azure 入口網站中開啟範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    範本外觀就像：

    ![HDInsight 網域建立樹系網域的虛擬機器](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. 輸入下列值：

    - **訂用帳戶**：選取 Azure 訂用帳戶。
    - **資源群組名稱**： 輸入資源群組名稱。  資源群組用來管理您與專案相關的 Azure 資源。
    - **位置**： 選取接近您的 Azure 位置。
    - **系統管理員使用者名稱**： 這是網域系統管理員使用者名稱。 此使用者不是您的 HDInsight 叢集的 HTTP 使用者帳戶。 這是您在本教學課程中使用的帳戶。
    - **系統管理員密碼**： 網域系統管理員輸入的密碼。
    - **網域名稱**： 網域名稱必須是兩部分名稱。 例如： contoso.com，或 contoso.local、 或 hdinsight.test。
    - **DNS 首碼**： 輸入的 DNS 前置詞
    - **PDC RDP 連接埠**: （在本教學課程中使用的預設值）
    - **BDC RDP 連接埠**: （在本教學課程中使用的預設值）
    - **成品位置**: （在本教學課程中使用的預設值）
    - **成品位置 SAS 權杖**: （保持空白本教學課程。）

花費 20 分鐘的時間建立的資源。

## <a name="setup-ldaps"></a>安裝程式 LDAPS

輕量型目錄存取通訊協定 (LDAP) 用來讀取和寫入至 AD。

**若要連接到 PDC 使用遠端桌面**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟資源群組，然後再開啟 主要網域控制站 (PDC) 虛擬機器。 預設 PDC 名稱是 adPDC。 
3. 按一下**連接**到 PDC 使用遠端桌面連線。

    ![HDInsight 網域連線 PDC 遠端桌面](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**若要新增 Active Directory 憑證服務**

4. 開啟**伺服器管理員**如果尚未開啟。
5. 按一下**管理**，然後按一下 **新增角色及功能**。

    ![HDInsight 網域新增角色及功能](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. 從"開始之前]，按一下 [**下一步**。
6. 選取 [角色型或功能型安裝]，然後按一下 [下一步]。
7. 選取 PDC，，然後按一下**下一步**。  預設 PDC 名稱是 adPDC。
8. 選取**Active Directory 憑證服務**。
9. 按一下**新增功能**從快顯對話方塊。
10. 遵循精靈的指示，使用預設的設定程序的其餘部分。
11. 按一下 [關閉]  即可關閉精靈。

**若要設定 AD 憑證**

1. 從 [伺服器管理員] 中，按一下黃色通知圖示，然後按一下**設定 Active Directory 憑證服務**。

    ![HDInsight 網域設定 AD 憑證](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. 按一下 * * 選取角色服務的左側，**憑證授權單位**，然後按一下 **下一步**。
3. 遵循精靈的指示，請使用程序的其餘預設設定 (按一下**設定**在最後一個步驟)。
4. 按一下 [關閉]  即可關閉精靈。

## <a name="optional-create-ad-users-and-groups"></a>（選擇性）建立 AD 使用者和群組

**若要在 AD 中建立使用者和群組**
1. 連接到 PDC 使用遠端桌面
1. 開啟**Active Directory 使用者和電腦**。
2. 在左窗格中，選取您的網域名稱。
3. 按一下**目前容器中建立新的使用者**在上方功能表中的圖示。

    ![HDInsight 網域建立的使用者](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. 請依照下列指示來建立幾個使用者。 例如，hiveuser1 和 hiveuser2。
5. 按一下**目前容器中建立新的群組**在上方功能表中的圖示。
6. 依照指示建立群組，稱為**HDInsightUsers**。  當您稍後在本教學課程中建立的 HDInsight 叢集時，會使用這個群組。

> [!IMPORTANT]
> 建立加入網域的 HDInsight 叢集之前，您必須重新 PDC 虛擬機器。

## <a name="create-an-hdinsight-cluster-in-the-vnet"></a>在 VNet 中建立的 HDInsight 叢集

在本節中，您可以使用 Azure 入口網站新增到您稍早在本教學課程中使用資源管理員範本建立虛擬網路的 HDInsight 叢集。 本文僅涵蓋加入網域的叢集設定的特定資訊。  如需一般資訊，請參閱[HDInsight 使用 Azure 入口網站中的 建立 linux 叢集](../hdinsight-hadoop-create-linux-clusters-portal.md)。  

**若要建立網域的 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 開啟您稍早在本教學課程中使用資源管理員範本建立的資源群組。
3. 新增資源群組的 HDInsight 叢集。
4. 選取**自訂**選項：

    ![HDInsight 網域自訂建立 選項](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    有不使用自訂組態選項的六個區段： 基本概念、 儲存體、 應用程式、 叢集大小、 進階的設定，以及摘要。
5. 在**基本概念**> 一節：

    - 叢集類型： 選取**企業安全性封裝**。 目前的企業安全性封裝，才可以啟用下列叢集類型： Hadoop、 互動式查詢，以及 Spark。

        ![HDInsight 網域聯結的企業安全性封裝](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - 叢集登入使用者名稱： 這是 Hadoop HTTP 使用者。 此帳戶與不同的網域系統管理員帳戶。
    - 資源群組： 選取您先前使用資源管理員範本所建立的資源群組。
    - 位置： 位置必須相同為您建立 vnet 時所使用的一個，而且網域控制站使用資源管理員範本。

6. 在**進階設定**> 一節：

    - 網域設定：

        ![HDInsight 網域聯結的進階的設定](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - 網域名稱： 輸入您在中使用的網域名稱[建立 Active Directory](#create-an-active-directory)。
        - 網域使用者名稱： 輸入您在中使用的 AD 系統管理員使用者名稱[建立 Active Directory](#create-an-active-directory)。
        - 組織單位： 請參閱範例螢幕擷取畫面。
        - LDAPS URL： 請參閱範例螢幕擷取畫面
        - 存取使用者群組： 輸入您在中建立的使用者群組名稱[建立 AD 使用者和群組](#optionally-createad-users-and-groups)
    - 虛擬網路： 選取您在建立虛擬網路[建立 Active Directory](#create-an-active-directory)。 範本中使用的預設名稱是**adVNET**。
    - 子網路： 範本中使用的預設名稱是**adSubnet**。



完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。 如需有關刪除叢集的指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](../hdinsight-administer-use-management-portal.md#delete-clusters)。

## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

