---
title: 已加入網域的 Azure HDInsight 架構 | Microsoft Docs
description: 了解如何規劃已加入網域的 HDInsight。
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8503534031dc5774e64c58edd3e158162a5a6aee
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110449"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>規劃 HDInsight 中已加入網域的 Azure Hadoop 叢集

標準 HDInsight 叢集是一個單一使用者叢集。 它適合大部分以小型應用程式團隊建置大型資料工作負載的公司。 每個使用者都可依需求具有本身專用的不同叢集，並且在不再需要時加以終結。 不過，許多企業都開始朝向一種模型發展，亦即由 IT 小組管理叢集，而由多個應用程式小組共用叢集。 因此，對於這些較大的企業，在 Azure HDInsight 中必須支援對叢集的多使用者存取。

在管理方面，HDInsight 依賴最廣受使用的識別提供者 – Active Directory (AD)。 藉由 HDInsight 與 [Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md) 的整合，您將可使用網域認證來存取叢集。 HDInsight 中的 VM 已加入為您提供的網域，因此已驗證的使用者將可順暢地使用在 HDInsight 上執行的所有服務 (Ambari、Hive 伺服器、Ranger、Spark Thrift 伺服器等)。 接著，系統管理員可使用 Apache Ranger 來建立強式授權原則，為叢集中的資源提供角色型存取控制。


## <a name="integrate-hdinsight-with-active-directory"></a>整合 HDInsight 與 Active Directory

開放原始碼 Hadoop 須依賴 Kerberos 來提供驗證和安全性。 因此，HDInsight 叢集節點都會加入由 AAD-DS 管理的網域。 針對叢集上的 Hadoop 元件，會設定 Kerberos 安全性。 系統會自動為每個 Hadoop 元件建立一個服務主體。 此外，針對每部已加入網域的電腦也會建立對應的電腦主體。 若要儲存這些服務和機器主體，必須在網域控制站 (AAD-DS) 內提供組織單位 (OU)，用來放置這些主體。 

總括而言，您必須設定一個具備下列條件的環境：

- Active Directory 網域 (由 AAD-DS 管理)
- 在 AAD-DS 中啟用安全 LDAP (LDAPS)。
- 從 HDInsight 的 VNET 到 AAD-DS VNET 的適當網路連線，以備您為其選擇個別的 Vnet 時使用。 HDI VNET 內的 VM 應可使用 VNET 對等互連來檢視 AAD-DS。 如果 HDI 和 AAD-DS 部署在相同的 VNET 中，則會自動提供連線，而不需要進一步的手動操作。
- 一個[在 AAD-DS 上建立](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)的組織單位 (OU)
- 一個具有下列權限的服務帳戶：
    - 在 OU 中建立服務主體。
    - 將機器加入網域並在 OU 中建立機器主體。

以下螢幕擷取畫面顯示在 contoso.com 中建立的 OU。 此螢幕擷取畫面中也顯示一些服務主體和電腦主體。

![已加入網域的 HDInsight 叢集 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>不同的網域控制站設定：
HDInsight 目前僅支援以 AAD-DS 作為可讓叢集藉由交談將叢集 Kerberize 的主要網域控制站。 不過，您也可以執行其他複雜的 AD 設定，只要該設定最終會啟用 AAD-DS 的 HDI 存取即可。

- **[Azure Active Directory Domain Services (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**：這項服務會提供與 Windows Server Active Directory 完全相容的受控網域。 Microsoft 會負責管理、修補及監視高可用性 (HA) 設定中的網域。 您可以放心地部署叢集，不必擔心網域控制站的維護問題。 使用者、群組和密碼都會從您的 Azure Active Directory(AAD) [從 AAD 到 AAD-DS 的單向同步] 進行同步，讓使用者可使用相同的公司認證來登入叢集。 如需詳細資訊，請參閱[如何使用 AAD-DS 設定已加入網域的 HDInsight 叢集](./apache-domain-joined-configure-using-azure-adds.md)。
- **內部部署 AD 或 IaaS VM 上的 AD**：如果您為網域進行內部部署 AD 或其他更複雜的 AD 設定，您可以使用 AD Connect 將這些識別同步至 AAD，然後在該 AD 租用戶上啟用 AAD-DS。 由於 Kerberos 須依賴密碼雜湊，因此您必須[對 AAD-DS 啟用密碼雜湊同步](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。 如果您使用與 AD 同盟服務 (ADFS) 的同盟，則可以選擇性地設定密碼雜湊同步，作為 ADFS 基礎結構失敗時的備用方式。 如需詳細資訊，請參閱[透過 AAD Connect 同步啟用密碼雜湊同步](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。單獨使用內部部署 AD 或 IaaS 虛擬機器上的 AD，而未使用 AAD 和 AAD-DS，並非 HDI 叢集網域加入支援的組態。

## <a name="next-steps"></a>後續步驟
* [設定已加入網域的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)。
* [為已加入網域的 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* [管理已加入網域的 HDInsight 叢集](apache-domain-joined-manage.md)。 
