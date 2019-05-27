---
title: 安裝和設定 Azure 成本管理的 AWS 成本和使用方式報表整合
description: 這篇文章會逐步引導您使用 Azure 成本管理中設定 AWS 成本和使用方式報表整合。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002131"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>安裝及設定 AWS 成本和使用方式報表整合

使用 Amazon Web Services (AWS) 成本和使用方式報表 （目前） 整合，您可以監視及控制您的 Azure 成本管理 AWS 費用。 整合可讓單一位置，其中您可以監視的 Azure 入口網站和 Azure 和 AWS 消費的控制項中。 這篇文章說明如何設定整合及設定，讓您使用 Azure 成本管理功能來分析成本，並檢閱預算。

成本管理程序 AWS 成本與使用量報表會儲存在 S3 貯體中，使用您的 AWS 存取認證，以取得報表定義並下載的報表 GZIP CSV 檔案。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中建立成本和使用方式報表

使用成本和使用方式報表是 AWS 建議的方式收集和處理 AWS 成本。 如需詳細資訊，請參閱 < [AWS 成本和使用量報告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文件。

使用**成本和使用方式報表**頁面的 計費與成本管理主控台，在 AWS 中建立成本和使用方式報告，執行下列步驟：

1. 登入 AWS 管理主控台，並開啟[計費與成本管理主控台](https://console.aws.amazon.com/billing)。
2. 在 [導覽] 窗格中，選取**成本和使用方式報表**。
3. 選取 **建立報表**。
4. 針對**報表名稱**，輸入報表的名稱。
5. 針對**額外的報告詳細資料**以包含每個資源識別碼中的報表，然後選取**包含資源識別碼**。
6. 針對**資料重新整理設定**、 選取您想要重新整理 AWS 適用於退款，信用額度，如果 AWS 成本和使用方式報表，或之後完成您的帳單支援費用，以您的帳戶。 當報表重新整理時，新的報表會上傳到 Amazon S3。 建議您在保留此設定。
7. 選取 [下一步] 。
8. 針對**S3 貯體**，選擇**設定**。
9. 在 [設定 S3 貯體] 對話方塊中，執行下列其中一項動作：
    1. 從下拉式清單中選取現有的貯體，然後選擇**下一步**。
    2. 輸入貯體名稱和您要建立新的貯體，並選擇的區域**下一步**。
10. 選取我確認此原則已更正，然後選擇 [儲存]。
11. （選擇性）報表路徑前置詞中，輸入您想要預留的報表名稱的報表路徑前置詞。
如果您未指定前置詞，則預設前置詞是您指定在步驟 4 中的報表和報表，以下列格式的日期範圍的名稱： `/report-name/date-range/`
12. 針對**時間單位**，選擇**每小時**。
13. 針對**報表的版本控制**，選擇是否要覆寫舊版的報表，或除了舊版傳遞報表的每個版本。
14. 針對**啟用資料整合**，沒有選取項目是必要。
15. 針對**壓縮**，選取**GZIP**。
16. 選取 [下一步] 。
17. 在檢閱過您的報表設定之後，請選取**檢閱並完成**。

    請注意報告名稱。 您將在稍後步驟中使用它。

可能需要最多 24 小時以開始將報表傳遞給您的 Amazon S3 貯體的 AWS。 傳遞啟動之後，AWS 更新 AWS 成本和使用方式報表檔一天至少一次。 您可以繼續設定您的 AWS 環境，而不需等待傳遞給啟動。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中建立的角色和原則

Azure 成本管理存取成本和使用方式報表所在的位置數次一天的 S3 貯體。 服務必須能夠存取認證，以檢查有新的資料。 若要允許存取它的成本管理的 AWS 中建立的角色和原則。

若要啟用以角色為基礎的存取，在成本管理 AWS 帳戶中，會建立在 AWS 主控台中的角色。 您必須能夠_角色 ARN_並_外部識別碼_從 AWS 主控台。 稍後，您在上使用這些**建立 AWS 連接器**在成本管理中的頁面。

使用 [建立新的角色] 精靈：

1. 登入 AWS 主控台，然後選取**Services**。
2. 在服務清單中，選取**IAM**。
3. 選取 **角色**，然後選取**Create Role**。
4. 在下一步 頁面上，選取**另一個 AWS 帳戶**。
5. 在 **帳戶識別碼**，輸入**432263259397**。
6. 在 **選項**，選取**需要外部 ID （最佳做法是第三方會假設此角色）**。
7. 在 **外部識別碼**，輸入外部識別碼。 外部識別碼是 AWS 角色和 Azure 成本管理之間的共用的密碼。 相同的外部識別碼也可以使用**新的連接器**在成本管理中的頁面。 例如，外部識別碼類似_Companyname1234567890123_。

    > [!NOTE]
    > 不會變更選取項目，如**要求 MFA**。 它應該維持已清除項目。
8. 完成時，選取 [下一步:**權限]**。
9. 選取 **建立原則**。 隨即開啟新的瀏覽器索引標籤。 這是您用來建立原則。
10. 選取 **選擇服務**。

設定成本和使用方式報表的權限：

1. 請輸入**成本和使用量報告**。
2. 選取 **存取層級** > **讀取** > **DescribeReportDefinitions**。 這個步驟可讓成本管理，以讀取哪些 CUR 報表定義，並判斷它們是否相符的報表定義的必要條件。
3. 選取 **新增額外的權限**。

設定您的 S3 貯體和物件的權限：

1. 選取 **選擇服務**。
2. 請輸入**S3**。
3. 選取 **存取層級** > **清單** > **ListBucket**。 此動作會取得 S3 貯體中的物件清單。
4. 選取 **存取層級** > **讀取** > **GetObject**。 這個動作可讓您下載的計費檔案。
5. 選取 **資源**。
6. 選取 **貯體 – 新增 ARN**。
7. 在 **貯體名稱**，輸入用來儲存目前檔案的貯體。
8. 選取 **物件 – 新增 ARN**。
9. 在 **貯體名稱**，輸入用來儲存目前檔案的貯體。
10. 在 **物件名稱**，選取**任何**。
11. 選取 **新增額外的權限**。

設定成本 explorer 的權限：

1. 選取 **選擇服務**。
2. 請輸入**成本 Explorer 服務**。
3. 選取 **成本 Explorer 服務的所有動作 (ce:\*)**。 此動作會驗證集合正確。
4. 選取 **新增額外的權限**。

新增 AWS 組織的權限：

1. 請輸入**組織**。
2. 選取 **存取層級** > **清單** > **ListAccounts**。 此動作取得的帳戶名稱。
3. 在 **檢閱原則**，輸入新原則的名稱。 請檢查您輸入正確的資訊，然後按**建立原則**。
4. 返回上一個索引標籤，並重新整理瀏覽器的網頁。 在 [搜尋] 列中，搜尋您的新原則。
5. 選取 **下一步： 檢閱**。
6. 輸入新角色的名稱。 請檢查您輸入正確的資訊，然後按**Create Role**。

    請注意，角色 ARN 和外部識別碼使用先前步驟中建立角色時。 您稍後會用它們在您設定 Azure 成本管理的連接器。

原則 JSON 看起來應該像下列的範例。 取代_bucketname_的 S3 貯體名稱。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>設定 Azure 中的新 AWS 連接器

您可以使用下列資訊來建立 AWS 連接器，並開始監視您的 AWS 成本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至**成本管理 + 計費** > **成本管理**。
3. 底下**設定**，選取**雲端連接器 （預覽）**。  
    ![顯示雲端連接器 （預覽） 的範例設定)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. 選取  **+ 新增**頂端的 建立連接器 頁面。
5. 在 **建立 AWS 連接器**頁面上，於**顯示名稱**，輸入您的連接器的名稱。  
    ![建立 AWS 連接器頁面範例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. （選擇性） 選取預設的管理群組。 它會儲存所有探索到連結的帳戶。 您可以將它設定更新版本。
7. 在 **計費**區段中，選取**自動收費 1%，在正式運作**如果您想要預覽到期時，確保持續的作業。 如果您選取 [自動] 選項時，您必須選取訂用帳戶計費。
8. 針對**角色 ARN**，輸入您設定在 AWS 中的角色時所使用的值。
9. 針對**外部識別碼**，輸入您設定在 AWS 中的角色時所使用的值。
10. 針對**報表名稱**，輸入您在 AWS 中建立的名稱。
11. 選取 **下一步**，然後選取**建立**。

可能需要幾個小時的新 AWS 範圍、 AWS 整合帳戶和連結的 AWS 帳戶，以及其成本資料才會出現。

建立連接器之後，我們建議您將存取控制指派給它。 要指派權限給使用者的新探索到的範圍：AWS 合併帳戶和連結的 AWS 帳戶。 建立連接器的使用者是連接器、 合併的帳戶和連結的所有帳戶的擁有者。

將連接器權限指派給使用者之後就會進行探索, 不到現有的 AWS 範圍中指派權限。 相反地，只有新連結的帳戶會指派權限。

## <a name="take-additional-steps"></a>採取額外的步驟

- [設定管理群組](../governance/management-groups/index.md#initial-setup-of-management-groups)，如果您還沒有這麼做。
- 請檢查新的範圍會加入至您的範圍選擇器。 選取 **重新整理**若要檢視最新的資料。
- 在 **雲端連接器**頁面上，選取您的連接器，然後選取**移至帳單帳戶**將連結的帳戶指派給管理群組。

## <a name="manage-cloud-connectors"></a>管理雲端連接器

當您選取連接器上**雲端連接器**頁面上，您可以：

- 選取 **移至帳單帳戶**若要檢視 AWS 資訊彙總帳戶。
- 選取 **存取控制**管理連接器角色指派。
- 選取 **編輯**更新連接器。 您無法變更 AWS 帳戶號碼，因為它會出現在 角色 ARN。 但是，您可以建立新的連接器。
- 選取 **確認**重新執行驗證測試，藉此確定成本管理，可以使用的連接器設定來收集資料。

![建立 AWS 連接器範例清單](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>設定 Azure 管理群組

若要建立一個單一的位置，來檢視跨雲端提供者資訊，您需要將您的 Azure 訂用帳戶和連結的 AWS 帳戶放在相同的管理群組中。 如果您尚未使用管理群組設定 Azure 環境，請參閱[初始的管理群組安裝](../governance/management-groups/index.md#initial-setup-of-management-groups)。

如果您想要不同的成本，您可以建立包含只是連結的 AWS 帳戶的管理群組。

## <a name="set-up-an-aws-consolidated-account"></a>設定 AWS 整合帳戶

計費和付款的多個的 AWS 帳戶，就會結合彙總的 AWS 帳戶。 它也會作為連結的 AWS 帳戶。

![AWS 範例詳細資料彙總帳戶](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

在頁面中，您可以：

- 選取 **更新**大量更新的 AWS 關聯連結的管理群組的帳戶。
- 選取 **存取控制**設定範圍的角色指派。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 的權限合併的帳戶

根據預設，合併的 AWS 帳戶的權限的帳戶建立後，根據 AWS 連接器權限設定。 連接器的建立者是擁有者。

使用管理的存取層級**存取層級**AWS 的頁面合併的帳戶。 不過，AWS 連結帳戶並不會繼承彙總的 AWS 帳戶的權限。

## <a name="set-up-an-aws-linked-account"></a>設定連結的 AWS 帳戶

連結的 AWS 帳戶是建立及管理 AWS 資源為目標的位置。 連結的帳戶也可做為安全性界限。

從這個頁面上，您可以：

- 選取 **更新**更新 AWS 的關聯連結的管理群組的帳戶。
- 選取 **存取控制**設定範圍的角色指派。

![[連結的 AWS 帳戶] 頁面的範例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 的權限已連結帳戶

根據預設，連結的 AWS 帳戶的權限建立後，根據 AWS 連接器權限設定。 連接器的建立者是擁有者。 使用管理的存取層級**存取層級**AWS 頁面連結的帳戶。 連結的 AWS 帳戶不會繼承彙總的 AWS 帳戶權限。

連結的 AWS 帳戶一律繼承其所屬的管理群組的權限。

## <a name="next-steps"></a>後續步驟

- 現在，您已安裝並設定 AWS 成本和使用方式報表整合，請繼續進行[管理的 AWS 成本和使用量](aws-integration-manage.md)。
- 如果您不熟悉成本分析，請參閱[探索及分析成本，以及成本分析](quick-acm-cost-analysis.md)快速入門。
- 如果您不熟悉 Azure 中的預算，請參閱[建立和管理 Azure 的預算](tutorial-acm-create-budgets.md)。
