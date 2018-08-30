---
title: 使用 Azure AD DS 來設定已加入網域的 HDInsight 叢集
description: 了解如何使用 Azure Active Directory Domain Services 來設定已加入網域的 HDInsight 叢集
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 07/17/2018
ms.openlocfilehash: 17924b0a00f4605d41492768b0124c583664aca6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042136"
---
# <a name="configure-a-domain-joined-hdinsight-cluster-by-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 來設定已加入網域的 HDInsight 叢集

已加入網域的叢集可在 Azure HDInsight 叢集上提供多使用者存取。 已加入網域的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。 

在此文章中，您會了解如何使用 Azure Active Directory Domain Services (Azure AD DS) 設定已加入網域的 HDInsight 叢集。

## <a name="enable-azure-ad-ds"></a>啟用 Azure AD DS

啟用 Azure AD DS 是必要條件，然後您才能建立加入網域的 HDInsight 叢集。 如需詳細資訊，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租用戶的系統管理員具有建立 Azure AD DS 執行個體的權限。 如果您使用 Azure Data Lake Storage Gen1 作為 HDInsight 的預設儲存體，則請確定 Data Lake Storage Gen1 的預設 Azure AD 租用戶與 HDInsight 叢集的網域相同。 因為 Hadoop 依賴 Kerberos 和基本驗證，所以必須針對將會存取叢集的使用者停用多重要素驗證。

在您佈建 Azure AD DS 執行個體後，請在 Azure Active Directory (Azure AD) 中建立具有適當權限的服務帳戶。 如果此服務帳戶已經存在，請重設其密碼，並等候其同步至 Azure AD DS。 此重設會導致建立 Kerberos 密碼雜湊，並可能需要 30 分鐘的時間同步處理到 Azure AD DS。 

此服務帳戶需要下列權限：

- 將機器加入網域，並將機器主體放入您在建立叢集期間指定的 OU 中。
- 在叢集建立期間，於您指定的 OU 中建立服務主體。

> [!NOTE]
> 因為 Apache Zeppelin 會使用網域名稱來驗證管理服務帳戶，所以服務帳戶「必須」具有與其 UPN 尾碼相同的網域名稱，Apache Zeppelin 才能正常運作。

若要深入了解 OU 及其管理方式，請參閱[在 Azure AD DS 受控網域上建立 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。 

安全 LDAP 適用於 Azure AD DS 受控網域。 如需詳細資訊，請參閱[針對 Azure AD DS 受控網域設定安全的 LDAP](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>建立已加入網域的 HDInsight 叢集

下一個步驟是使用 Azure AD DS 以及在上一節建立的服務帳戶來建立 HDInsight 叢集。

將 Azure AD DS 執行個體和 HDInsight 叢集放在相同的 Azure 虛擬網路中比較容易。 如果您選擇將它們放在不同的虛擬網路中，則必須將這些虛擬網路設為對等，讓 HDInsight VM 能夠看到要加入 VM 的網域控制站。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

建立已加入網域的 HDInsight 叢集時，必須提供下列參數：

- **網域名稱**：與 Azure AD DS 相關聯的網域名稱。 例如 contoso.onmicrosoft.com。

- **網域使用者名稱**：上一節在 Azure AD DS DC 受控網域中建立的服務帳戶。 例如 hdiadmin@contoso.onmicrosoft.com。 此網域使用者將是這個 HDInsight 叢集的系統管理員。

- **網域密碼**：服務帳戶的密碼。

- **組織單位**︰HDInsight 叢集要搭配使用的組織單位 (OU) 的辨別名稱。 例如 OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com。 如果這個 OU 不存在，HDInsight 叢集會嘗試使用服務帳戶具有的權限建立 OU。 例如，如果服務帳戶位在 Azure AD DS 的 Administrators 群組當中，就會具有適當的權限來建立 OU。 否則，您必須先建立 OU，然後提供服務帳戶該 OU 的完整控制權限。 如需詳細資訊，請參閱[在 Azure AD DS 受控網域上建立 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。

    > [!IMPORTANT]
    > 在 OU 後面包含以逗號分隔的所有 DC (例如 OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com)。

- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636。

    > [!IMPORTANT]
    > 輸入完整的 URL，包括 "ldaps://" 和連接埠號碼 (:636)。

- **存取使用者群組**︰您要將其使用者同步至叢集的安全性群組。 例如，HiveUsers。 如果要指定多個使用者群組，請以分號 (;) 來分隔。 群組必須在佈建之前存在於目錄中。 如需詳細資訊，請參閱[在 Azure Active Directory 中建立群組和新增使用者](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 如果群組不存在，就會發生錯誤：「在 Active Directory 中找不到群組 HiveUsers。」

以下螢幕擷取畫面顯示 Azure 入口網站上中的設定：

   ![已加入 Azure HDInsight 網域的 Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

