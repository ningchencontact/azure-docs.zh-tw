---
title: 使用 Grafana 的 Azure 資料總管中的資料視覺化
description: 在此操作說明中，您將了解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後從叢集範例中將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 135f8f1c9c352f9d2307a8bf9ad1bec892aac179
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399920"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>在 Grafana 中從 Azure 資料總管將資料視覺化

Grafana 是分析平台，可讓您查詢和視覺化資料，然後根據您的視覺效果建立並共用儀表板。 Grafana 提供 Azure 資料總管「外掛程式」  ，可讓您從 Azure 資料總管連線到資料並加以視覺化。 在本文中，您將了解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後從叢集範例中將資料視覺化。

## <a name="prerequisites"></a>必要條件

若要完成本操作說明，您需要下列項目：

* 適用於您作業系統的 [Grafana 5.3.0 版或更新版本](https://docs.grafana.org/installation/)

* 適用於 Grafana 的 [Azure 資料總管外掛程式](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* 包含 StormEvents 範例資料的叢集。 如需詳細資訊，請參閱[快速入門：建立 Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)及[將範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>設定資料來源

執行下列步驟，即可將 Azure 資料總管設定為 Grafana 的資料來源。 我們將在這一節中詳細說明這些步驟：

1. 建立 Azure Active Directory (Azure AD) 服務主體。 Grafana 會使用服務主體來存取 Azure 資料總管服務。

1. 將 Azure AD 服務主體新增至Azure 資料總管資料庫中的「檢視者」  角色。

1. 根據 Azure AD 服務主體中的資訊指定 Grafana 連線屬性，然後測試連線。

### <a name="create-a-service-principal"></a>建立服務主體

您可以在 [Azure 入口網站](#azure-portal)中或使用 [Azure CLI](#azure-cli) 命令列體驗來建立服務主體。 不論使用哪一種方法，建立服務主體後，您將取得可用於四個連線屬性 (後續步驟會用到) 的值。

#### <a name="azure-portal"></a>Azure 入口網站

1. 若要建立服務主體，請依照 [Azure 入口網站文件](/azure/active-directory/develop/howto-create-service-principal-portal)中的指示。

    1. 在[將應用程式指派給角色](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)的區段中，將**讀者**角色類型指派給您的 Azure 資料總管叢集。

    1. 在[取得值以便登入](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)區段中，複製步驟中包含的三個屬性值：**目錄識別碼**(租用戶識別碼)、**應用程式識別碼**及**密碼**。

1. 在 Azure 入口網站中，選取 [訂用帳戶]  ，然後對您已在其中建立服務主體的訂用帳戶複製其識別碼。

    ![訂用帳戶識別碼 - 入口網站](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. 建立服務主體。 設定適當的範圍和 `reader` 角色類型。

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    如需詳細資訊，請參閱[使用 Azure CLI 建立 Azure 服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)。

1. 此命令會傳回結果集，如下所示。 複製三個屬性值：**應用程式識別碼**、**密碼**和**租用戶**。

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. 取得您的訂用帳戶清單。

    ```azurecli
    az account list --output table
    ```

    複製正確的訂用帳戶識別碼。

    ![訂用帳戶識別碼 - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>將服務主體新增至檢視者角色

您現在已有服務主體，您可以將其新增至Azure 資料總管資料庫中的「檢視者」  角色。 您可以在 Azure 入口網站中的 [權限]  底下執行此工作，或在 [查詢]  底下使用管理命令來執行。

#### <a name="azure-portal---permissions"></a>Azure 入口網站 - 權限

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。

1. 在 [概觀]  區段中，選取具有 StormEvents 範例資料的資料庫。

    ![選取資料庫](media/grafana/select-database.png)

1. 選取 [權限]  ，然後 [新增]  。

    ![資料庫權限](media/grafana/database-permissions.png)

1. 在 [新增資料庫權限]  底下，選取 [檢視者]  角色，然後**選取主體**。

    ![新增資料庫權限](media/grafana/add-permission.png)

1. 搜尋您建立的服務主體 (此範例顯示的主體為 **mb grafana**)。 選取主體，然後**選取**。

    ![在 Azure 入口網站中管理權限](media/grafana/new-principals.png)

1. 選取 [ **儲存**]。

    ![在 Azure 入口網站中管理權限](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>管理命令 - 查詢

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集，然後選取 [查詢]  。

    ![查詢](media/grafana/query.png)

1. 在查詢視窗中執行下列命令。 從 Azure 入口網站或 CLI 使用應用程式識別碼和租用戶識別碼。

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    此命令會傳回結果集，如下所示。 在此範例中，第一個資料列是資料庫中的現有使用者，第二個資料列是剛才新增的服務主體。

    ![結果集](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>指定屬性並測試連線

將服務主體指派給「檢視者」  角色後，您現在要在 Grafana 執行個體中指定屬性，並測試與 Azure 資料總管的連線。

1. 在 Grafana 的左側功能表中，選取齒輪圖示，然後選取 [資料來源]  。

    ![資料來源](media/grafana/data-sources.png)

1. 選取 [新增資料來源]  。

1. 在 [資料來源] / [新增]  頁面上，輸入資料來源名稱，然後選取 [Azure 資料總管資料來源]  類別。

    ![連線名稱和類型](media/grafana/connection-name-type.png)

1. 以下列格式輸入您的叢集名稱： https://{ClusterName}.{Region}.kusto.windows.net。 從 Azure 入口網站或 CLI 輸入其他值。 請參閱下圖底下的對應表。

    ![連線屬性](media/grafana/connection-properties.png)

    | Grafana UI | Azure 入口網站 | Azure CLI |
    | --- | --- | --- |
    | 訂用帳戶識別碼 | 訂用帳戶識別碼 | SubscriptionId |
    | 租用戶識別碼 | 目錄識別碼 | tenant |
    | 用戶端識別碼 | 應用程式識別碼 | appId |
    | 用戶端密碼 | 密碼 | password |
    | | | |

1. 選取 [儲存並測試]  。

    如果測試成功，請移至下一節。 如果您遇到任何問題，請檢查您在 Grafana 中指定的值，並檢閱先前的步驟。

## <a name="visualize-data"></a>顯現資料

現在您已將 Azure 資料總管設為 Grafana 的資料來源，是時候將資料視覺化了。 我們將在此示範基本範例，但您可以做的事還很多。 我們建議您查看[撰寫 Azure 資料總管的查詢](write-queries.md)，以取得其他針對範例資料集執行的查詢範例。

1. 在 Grafana 的左側功能表中，選取加號圖示，然後選取 [儀表板]  。

    ![建立儀表板](media/grafana/create-dashboard.png)

1. 在 [新增]  索引標籤底下，選取 [圖表]  。

    ![新增圖表](media/grafana/add-graph.png)

1. 在 [圖表] 面板中，依序選取 [面板標題]  和 [編輯]  。

    ![編輯面板](media/grafana/edit-panel.png)

1. 在面板底部，選取 [資料來源]  ，然後選取您所設定的資料來源。

    ![選取資料來源](media/grafana/select-data-source.png)

1. 在 [查詢] 窗格中，複製下列查詢，然後選取 [執行]  。 查詢會包含範例資料集的每日事件計數。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![執行查詢](media/grafana/run-query.png)

1. 圖表未顯示任何結果，因為其預設的資料範圍是過去六小時。 在頂端功能表上，選取 [過去 6 小時]  。

    ![過去六小時](media/grafana/last-six-hours.png)

1. 指定自訂範圍以涵蓋 2007 年，也就是 StormEvents 範例資料集包含的年份。 選取 [套用]  。

    ![自訂日期範圍](media/grafana/custom-date-range.png)

    現在圖表會顯示 2007 中的資料，並依據日期分佈。

    ![已完成的圖表](media/grafana/finished-graph.png)

1. 在最上層功能表中，選取儲存圖示： ![[儲存] 圖示](media/grafana/save-icon.png)上也提供本文中使用的原始碼。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)

* [教學課程：在 Power BI 中從 Azure 資料總管將資料視覺化](visualize-power-bi.md)