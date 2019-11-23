---
title: Synchronize Azure Active Directory users to HDInsight cluster
description: Synchronize authenticated users from Azure Active Directory to an HDInsight cluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: acacb9c10250d43e22b5b5b1d073b18461561512
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406869"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>將 Azure Active Directory 使用者同步至 HDInsight 叢集

[有企業安全性套件 (ESP) 的 HDInsight 叢集](hdinsight-domain-joined-introduction.md)會搭配 Azure Active Directory (Azure AD) 使用者使用增強式驗證，也會使用*角色型存取控制* (RBAC) 原則。 As you add users and groups to Azure AD, you can synchronize the users who need access to your cluster.

## <a name="prerequisites"></a>必要條件

如果您尚未這樣做，請[建立有企業安全性套件的 HDInsight 叢集](hdinsight-domain-joined-configure.md)。

## <a name="add-new-azure-ad-users"></a>新增 Azure AD 使用者

若要檢視您的主機，請開啟 Ambari Web UI。 每個節點會以新的自動升級設定進行更新。

1. From the [Azure portal](https://portal.azure.com), navigate to the Azure AD directory associated with your ESP cluster.

2. 從左側功能表中選取 [所有使用者]，然後選取 [新增使用者]。

    ![Azure portal users and groups all](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. 完成新增使用者表單。 選取您建立用於指派叢集型權限的群組。 在此範例中，建立名為 "HiveUsers" 的群組，您可以對其指派新使用者。 用於建立 ESP 叢集的[範例指示](hdinsight-domain-joined-configure.md)包含新增兩個群組，分別是 `HiveUsers` 和 `AAD DC Administrators`。

    ![Azure portal user pane select groups](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. 選取 [建立]。

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>使用 Apache Ambari REST API 來同步使用者

屆時會同步在叢集建立程序期間指定的使用者群組。 使用者同步會每小時自動進行一次。 若要立即同步使用者或同步在叢集建立期間指定之群組以外的群組，請使用 Ambari REST API。

下列方法會搭配 Ambari REST API 使用 POST。 如需詳細資訊，請參閱[使用 Apache Ambari REST API 來管理 HDInsight 叢集](hdinsight-hadoop-manage-ambari-rest-api.md)。

1. Use [ssh command](hdinsight-hadoop-linux-use-ssh-unix.md) to connect to your cluster. 編輯下列命令並將 `CLUSTERNAME` 取代為您叢集的名稱，然後輸入該命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 驗證之後，輸入下列命令：

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    回應應該如下所示：

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. 若要查看同步狀態，請執行新的 `curl` 命令：

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    回應應該如下所示：

    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. This result shows that the status is **COMPLETE**, one new user was created, and the user was assigned a membership. 在此範例中，會將使用者指派給 "HiveUsers" 已同步的 LDAP 群組，因為使用者已新增至 Azure AD 中的該相同群組。

    > [!NOTE]  
    > The previous method only synchronizes the Azure AD groups specified in the **Access user group** property of the domain settings during cluster creation. 如需詳細資訊，請參閱[建立 HDInsight 叢集](domain-joined/apache-domain-joined-configure.md)。

## <a name="verify-the-newly-added-azure-ad-user"></a>確認新增的 Azure AD 使用者

開啟 [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) 以確認已新增新的 Azure AD 使用者。 藉由瀏覽至 **`https://CLUSTERNAME.azurehdinsight.net`** 來存取 Ambari Web UI。 輸入叢集系統管理員使用者名稱和密碼。

1. 從 Ambari 儀表板中，選取 [管理員] 功能表底下的 [管理 Ambari]。

    ![Apache Ambari dashboard Manage Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. 在分頁左側，選取 [使用者 + 群組管理] 功能表群組底下的 [使用者]。

    ![HDInsight users and groups menu](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. 新的使用者應該會列在 [使用者] 資料表中。 類型設定為 `LDAP` 而不是 `Local`。

    ![HDInsight aad users page overview](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>以新的使用者身分登入 Ambari

當新的使用者 (或任何其他網域使用者) 登入 Ambari 時，他們會使用其完整的 Azure AD 使用者名稱和網域認證。  Ambari 會顯示使用者別名，這是使用者在 Azure AD 中的顯示名稱。
新的範例使用者具有使用者名稱 `hiveuser3@contoso.com`。 在 Ambari 中，這個新的使用者會顯示為 `hiveuser3`，但是使用者是以 `hiveuser3@contoso.com` 身分登入 Ambari。

## <a name="see-also"></a>請參閱

* [在有 ESP 的 HDInsight 中設定 Apache Hive 原則](hdinsight-domain-joined-run-hive.md)
* [管理具有 ESP 的 HDInsight 叢集](hdinsight-domain-joined-manage.md)
* [授權使用者存取 Apache Ambari](hdinsight-authorize-users-to-ambari.md)
