---
title: 使用識別碼代理程式（預覽）進行認證管理-Azure HDInsight
description: 瞭解 HDInsight 識別碼訊息代理程式，以簡化已加入網域的 Apache Hadoop 叢集的驗證。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: f14cbef2ab568962601b3a407fa979e8f982598d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483007"
---
# <a name="use-id-broker-preview-for-credential-management"></a>使用識別碼代理程式（預覽）進行認證管理

本文說明如何在 Azure HDInsight 中設定和使用識別碼訊息代理程式功能。 您可以使用這項功能透過 Azure 多重要素驗證登入 Apache Ambari，並取得必要的 Kerberos 票證，而不需要在 Azure Active Directory Domain Services （Azure AD DS）中使用密碼雜湊。

## <a name="overview"></a>概觀

在下列案例中，識別碼 Broker 會簡化複雜的驗證程式：

* 您的組織會依賴同盟來驗證使用者，以存取雲端資源。 先前，若要使用 HDInsight 企業安全性套件（ESP）叢集，您必須啟用從內部部署環境到 Azure Active Directory 的密碼雜湊同步處理。 對於某些組織來說，這項需求可能很艱難或不是必要的。

* 您正在建立使用依賴不同驗證機制之技術的解決方案。 例如，Apache Hadoop 和 Apache Ranger 依賴 Kerberos，而 Azure Data Lake Storage 依賴 OAuth。

識別碼代理人提供統一的驗證基礎結構，並移除將密碼雜湊同步至 Azure AD DS 的需求。 識別碼代理程式是由 Windows Server VM （識別碼代理程式節點）上執行的元件以及叢集閘道節點所組成。 

下圖顯示已啟用識別碼代理人之後，所有使用者的驗證流程，包括同盟使用者：

![識別碼訊息代理程式的驗證流程](./media/identity-broker/identity-broker-architecture.png)

識別碼代理程式可讓您使用多重要素驗證來登入 ESP 叢集，而不需要提供任何密碼。 如果您已經登入其他 Azure 服務（例如 Azure 入口網站），您可以使用單一登入（SSO）體驗來登入您的 HDInsight 叢集。

## <a name="enable-hdinsight-id-broker"></a>啟用 HDInsight 識別碼代理程式

若要建立已啟用識別碼代理程式的 ESP 叢集，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 遵循 ESP 叢集的基本建立步驟。 如需詳細資訊，請參閱[建立具有 ESP 的 HDInsight](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)叢集。
1. 選取 [**啟用 HDINSIGHT 識別碼訊息代理**程式]。

識別碼訊息代理程式功能會將一個額外的 VM 新增至叢集。 此 VM 是識別碼訊息代理程式節點，包含支援驗證的伺服器元件。 識別碼代理人節點已加入網域至 Azure AD DS 網域。

![啟用識別碼訊息代理程式的選項](./media/identity-broker/identity-broker-enable.png)

## <a name="tool-integration"></a>工具整合

HDInsight [IntelliJ 外掛程式](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib)已更新為支援 OAuth。 您可以使用此外掛程式來連接到叢集並提交工作。

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Azure AD DS 中沒有密碼雜湊的 SSH 存取

啟用識別碼代理人之後，您仍然需要在使用網域帳戶的 SSH 案例中，儲存在 Azure AD DS 中的密碼雜湊。 若要透過 SSH 連線到已加入網域的 VM，或執行 `kinit` 命令，您必須提供密碼。 

SSH 驗證需要在 Azure AD DS 中使用雜湊。 如果您只想要在系統管理案例中使用 SSH，您可以建立一個僅限雲端的帳戶，並使用它來透過 SSH 連線到叢集。 其他使用者仍然可以使用 Ambari 或 HDInsight 工具（例如 IntelliJ 外掛程式），而不需要在 Azure AD DS 中提供密碼雜湊。

## <a name="next-steps"></a>後續步驟

* [使用 Azure Active Directory Domain Services 設定具有企業安全性套件的 HDInsight 叢集](apache-domain-joined-configure-using-azure-adds.md)
* [將 Azure Active Directory 使用者同步至 HDInsight 叢集](../hdinsight-sync-aad-users-to-cluster.md)
* [監視叢集效能](../hdinsight-key-scenarios-to-monitor.md)
