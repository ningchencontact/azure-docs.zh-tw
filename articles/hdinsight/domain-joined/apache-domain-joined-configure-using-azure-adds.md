---
title: 使用 AAD-DS 設定已加入網域的 HDInsight 叢集
description: 了解如何使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8064940e0d0f035010a2521752d6f32f3f9ccd9f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849821"
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集

已加入網域的叢集可在 HDInsight 上提供多使用者存取。 已加入網域的 HDInsight 叢集會與網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。 

在此文章中，您會了解如何使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集。

## <a name="create-azure-adds"></a>建立 Azure ADDS

啟用 Azure AD Domain Services (AAD-DS) 是必要條件，然後您才能建立加入網域的 HDInsight 叢集。 若要啟用 AAD-DS 執行個體，請參閱[如何使用 Azure 入口網站啟用 AAD-DS](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租用戶的系統管理員具有建立 AAD-DS 執行個體的權限。 如果您使用 Azure Data Lake Storage (ADLS) 作為 HDInsight 的預設儲存體，則請確定 ADLS 的預設 Azure AD 租用戶與 HDInsight 叢集的網域相同。 由於 Hadoop 依賴 Kerberos 和基本驗證，因此必須為具有叢集存取權的使用者停用多重要素驗證。

佈建 AAD-DS 執行個體之後，您必須使用適當的權限在 AAD 中建立服務帳戶 (將同步到 AAD-DS)。 如果此服務帳戶已存在，您必須重設其密碼並等候其同步到 AAD-DS 為止 (此重設將導致建立 Kerberos 密碼雜湊，最多可能需要 30 分鐘來同步到 AAD-DS)。 此服務帳戶應該具有下列權限：

- 將機器加入網域，並將機器主體放入您在建立叢集期間指定的 OU 中。
- 在叢集建立期間，於您指定的 OU 中建立服務主體。

> [!NOTE]
> 因為 Apache Zeppelin 會使用網域名稱來驗證管理服務帳戶，所以服務帳戶「必須」具有與其 UPN 尾碼相同的網域名稱，Apache Zeppelin 才能正常運作。

若要深入了解 OU 和如何管理它們，請參閱[在 AAD-DS 上建立 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)。 

需要適用於 AAD-DS 受控網域的安全 LDAP。 若要啟用安全 LDAP，請參閱[如何針對 AAD-DS 受控網域設定安全 LDAP (LDAPS)](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>建立已加入網域的 HDInsight 叢集

下一個步驟是使用 AAD-DS 以及在上一節建立的服務帳戶來建立 HDInsight 叢集。

將 AAD-DS 和 HDInsight 叢集放在相同的 Azure 虛擬網路 (VNet) 中比較容易。 如果您選擇將它們放在不同的 VNet 中，則必須將這些 VNet 設為對等，讓 HDI VM 能夠看到要加入 VM 的網域控制站。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

建立已加入網域的 HDInsight 叢集時，必須提供下列參數：

- **網域名稱**：與 AAD-DS 建立關聯的網域名稱。 例如，contoso.onmicrosoft.com。
- **網域使用者名稱**：上一節在受控網域中建立的服務帳戶。 例如： hdiadmin@contoso.onmicrosoft.com。 此網域使用者將是這個 HDInsight 叢集的系統管理員。
- **網域密碼**：服務帳戶的密碼。
- **組織單位**︰HDInsight 叢集要搭配使用的組織單位 (OU) 的辨別名稱。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com。 如果不存在這個 OU，HDInsight 叢集會嘗試使用服務帳戶具有的權限建立 OU。 (例如，如果服務帳戶位在 AAD-DS 系統管理員群組中，它就具有建立 OU 的適當權限，否則您可能需要先建立 OU，並先提供該 OU 的完整控制權給服務帳戶 - 請參閱[在 AAD-DS 上建立 OU](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md))。

    > [!IMPORTANT]
    > 請務必在 OU 後面包含以逗號分隔的所有 DC (例如 OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com)。

- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636。

    > [!IMPORTANT]
    > 請輸入完整的 URL，包括 ldaps:// 和連接埠號碼 (: 636)

- **存取使用者群組**︰您要將其使用者同步至叢集的安全性群組。 例如，HiveUsers。 如果要指定多個使用者群組，以逗號 (,) 分隔它們。
 
以下螢幕擷取畫面顯示 Azure 入口網站上中的設定：

![已加入 Azure HDInsight 網域的 Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

