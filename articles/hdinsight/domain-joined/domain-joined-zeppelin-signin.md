---
title: 無法登入 Azure HDInsight 中的 Zeppelin
description: 無法登入 Azure HDInsight 中的 Zeppelin
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977400"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>案例:無法登入 Azure HDInsight 中的 Apache Zeppelin

本文說明與 Azure HDInsight 叢集互動時, 問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

變更 active directory 中的 [新增密碼] 之後, 無法登入 Apache Zeppelin。

## <a name="cause"></a>原因

檔案中`activeDirectoryRealm.systemUsername` `shiro_ini`提及的使用者已變更 active directory 密碼。

## <a name="resolution"></a>解決方法

1. 藉由在 Ambari 的 Zeppelin `activeDirectoryRealm.systemPassword = <new password>` `shiro_ini`設定中包含, 確認變更的密碼是根本原因。 `activeDirectoryRealm.hadoopSecurityCredentialPath`移除設定。 以下是的`shiro ini`位置。

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. 如果使用者現在可以在步驟1之後登入 Zeppelin, 請使用新`jceks`的密碼建立新的檔案, 並`activeDirectoryRealm.hadoopSecurityCredentialPath`以新的檔案取代。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)連接-官方 Microsoft Azure 帳戶, 以改善客戶體驗。 將 Azure 社區連接到正確的資源: 解答、支援和專家。

* 如果您需要更多協助, 您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**], 或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊, 請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權, 而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
