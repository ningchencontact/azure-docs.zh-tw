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
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 20d6dbad6fa1914c8b12f47bb48f6efba3895887
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>規劃 HDInsight 中已加入網域的 Azure Hadoop 叢集

標準 HDInsight 叢集是一個單一使用者叢集。 它適合大部分以小型應用程式團隊建置大型資料工作負載的公司。 隨著 Hadoop 日益普及，許多企業都開始朝向一種模型發展，亦即由 IT 小組管理叢集，而由多個應用程式小組共用叢集。 因此，Azure HDInsight 中呼聲最高的就是涉及多使用者叢集的功能。

HDInsight 不會建置自己的多使用者驗證和授權，而是依賴最受歡迎的識別提供者 – Active Directory (AD)。 AD 的強大安全性功能可用來管理 HDInsight 中的多使用者驗證。 藉由整合 HDInsight 與 AD，您可以使用 AD 認證來與叢集通訊。 HDInsight 中的 VM 已加入您的 AD 網域，而這也是 HDInsight 將 AD 使用者對應至本機 Hadoop 使用者的方式，可讓已通過驗證的使用者順暢地使用在 HDInsight 上執行的所有服務 (Ambari、Hive 伺服器、Ranger、Spark Thrift 伺服器等)。 系統管理員可以接著使用 Apache Ranger 來建立強式授權原則，為 HDInsight 中的資源提供角色型存取控制。


## <a name="integrate-hdinsight-with-active-directory"></a>整合 HDInsight 與 Active Directory

將 HDInsight 與 Active Directory 整合時，HDInsight 叢集節點會加入 AD 網域。 針對叢集上的 Hadoop 元件，會設定 Kerberos 安全性。 在 Active Directory 上，會針對每個 Hadoop 元件建立一個服務主體。 此外，針對每部已加入網域的電腦也會建立對應的電腦主體。 這些服務主體和電腦主體可能打亂您的 Active Directory 佈局。 因此，必須在 Active Directory 內提供「組織單位」(OU) 來放置這些主體。 

總括而言，您需要設定一個具備下列條件的環境：

- 一個已設定 LDAPS 的 Active Directory 網域控制站。
- 可從 HDInsight 虛擬網路連至您 Active Directory 網域控制站的連線。
- 一個已在 Active Directory 上建立的「組織單位」(OU)。
- 一個具有下列權限的服務帳戶：

    - 在 OU 中建立服務主體。
    - 將電腦加入網域並在 OU 中建立電腦主體。

以下螢幕擷取畫面顯示在 contoso.com 中建立的 OU。此螢幕擷取畫面中也顯示一些服務主體和電腦主體。

![已加入網域的 HDInsight 叢集 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="the-way-of-bringing-your-own-active-directory-domain-controllers"></a>自備 Active Directory 網域控制站的方式

- **Azure Active Directory Domain Services**：這項服務會提供與 Windows Server Active Directory 完全相容的受控 Active Directory 網域。 Microsoft 會負責管理、修補及監視 AD 網域。 您可以放心地部署叢集，不必擔心網域控制站的維護問題。 使用者、群組和密碼都會從 Azure Active Directory 同步過來，讓使用者使用其公司認證來登入叢集。 如需詳細資訊，請參閱[使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集](./apache-domain-joined-configure-using-azure-adds.md)。


## <a name="next-steps"></a>後續步驟
* 若要管理已加入網域的 HDInsight 叢集，請參閱[管理已加入網域的 HDInisight 叢集](apache-domain-joined-manage.md)。
* 若要設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 若要在已加入網域的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。
