---
title: "已加入網域的 Azure HDInsight 架構 | Microsoft Docs"
description: "了解如何規劃已加入網域的 HDInsight。"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2017
ms.author: saurinsh
ms.openlocfilehash: eca019fa5e7866ed6281e8cfee105ba1d99249bc
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>規劃 HDInsight 中已加入網域的 Azure Hadoop 叢集

標準的 HDInsight 叢集會為單一使用者叢集。 它適合大部分以小型應用程式團隊建置大型資料工作負載的公司。 Hadoop 取得受歡迎情況看出，許多企業啟動朝向所在叢集所管理的 IT 團隊和多個應用程式的小組共用叢集模型。 因此，Azure HDInsight 中呼聲最高的就是涉及多使用者叢集的功能。

HDInsight 不會建置自己的多使用者驗證和授權，而是依賴最受歡迎的識別提供者 – Active Directory (AD)。 在 AD 中的功能強大的安全性功能可以用來管理 HDInsight 中的多使用者驗證。 藉由整合 HDInsight 與 AD，您可以使用 AD 認證來與叢集通訊。 HDInsight 中的 Vm 是您的 ad 加入網域，而且是如何 HDInsight AD 將使用者對應到本機的 Hadoop 使用者，讓所有 HDInsight 上執行的服務 (Ambari 登錄區伺服器，廣，Spark thrift 伺服器和其他人) 驗證的使用者順暢地運作。 然後，系統管理員可以建立使用 Apache Ranger HDInsight 中的資源提供以角色為基礎的存取控制的強式的授權原則。


## <a name="integrate-hdinsight-with-active-directory"></a>整合 HDInsight 與 Active Directory

當整合 HDInsight 與 Active Directory，HDInsight 叢集節點若為加入網域的 AD 網域。 在叢集上的 Hadoop 元件已設定 Kerberos 安全性。 針對每個 Hadoop 元件中，Active Directory 上建立服務主體。 針對每個已加入網域的機器，也會建立對應的主要機器。 這些服務主體與機器主體可以干擾您的 Active Directory。 如此一來，它需要提供組織單位 (OU) 中 Active Directory 中，這些主體放置的位置。 

若要總括而言，您需要設定的環境：

- 透過 LDAPS 設定使用 Active Directory 網域控制站。
- 從 HDInsight 的虛擬網路與 Active Directory 網域控制站的連線。
- 建立 Active Directory 組織單位。
- 服務帳戶具有權限：

    - 在 OU 中建立服務主體。
    - 將機器加入網域和 OU 中建立電腦原則。

下列螢幕擷取畫面會顯示在 contoso.com 建立 OU。部分服務主體與機器主體中螢幕擷取畫面也會顯示。

![網域加入的 HDInsight 叢集 ou](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="three-ways-of-bringing-your-own-active-directory-domain-controllers"></a>攜帶您自己的 Active Directory 網域控制站的三種方式

有三種方式，您可以將 Active Directory 網域控制站來建立加入網域的 HDInsight 叢集。 

- **Azure Active Directory 網域服務**： 此服務會提供受管理的 Active Directory 網域，也就是與 Windows Server Active Directory 完全相容。 Microsoft 會負責管理、修補及監視 AD 網域。 您可以放心地部署叢集，不必擔心網域控制站的維護問題。 使用者、 群組和密碼會同步處理從 Azure Active Directory，讓叢集中使用其公司認證登入的使用者。 如需詳細資訊，請參閱[使用 Azure Active Directory 網域服務設定網域的 HDInsight 叢集](./apache-domain-joined-configure-using-azure-adds.md)。

- **Azure IaaS Vm 上的 active Directory**： 此選項，在您部署和管理您自己在 Azure IaaS Vm 上的 Windows Server Active Directory 網域。 如需詳細資訊，請參閱[設定網域加入的沙箱環境](./apache-domain-joined-configure.md)。

- **在內部部署 Active Directory**： 在本選項中，您必須整合 HDInsight 與您在內部部署 Active Directory 網域控制站。


## <a name="next-steps"></a>後續步驟
* 若要設定已加入網域的 HDInsight 叢集，請參閱[設定已加入網域的 HDInisight 叢集](apache-domain-joined-configure.md)。
* 若要管理已加入網域的 HDInsight 叢集，請參閱[管理已加入網域的 HDInisight 叢集](apache-domain-joined-manage.md)。
* 若要設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 若要在已加入網域的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。
