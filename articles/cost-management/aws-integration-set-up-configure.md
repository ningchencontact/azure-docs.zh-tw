---
title: 安裝和設定 Azure 成本管理的 AWS 成本和使用方式報表整合
description: 本文逐步引導您使用 Azure 成本管理中設定 AWS 成本和使用方式報表整合。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: a7a020284f44eda0da62f307866c74b0a8df493d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205707"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>安裝及設定 AWS 成本和使用方式報表整合

使用 Amazon Web Services 成本和使用方式報表的整合，您可以監視並控制您的 Azure 成本管理 AWS 費用。 整合可讓單一位置，其中您可以監視的 Azure 入口網站和 Azure 和 AWS 消費的控制項中。 這篇文章說明如何設定整合及設定，讓您使用成本管理功能來分析成本，並檢閱預算。

成本管理程序 AWS 成本與使用量報表會儲存在 S3 貯體中，使用您的 AWS 存取認證，以取得報表定義並下載的報表 GZIP CSV 檔案。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中建立成本和使用方式報表

使用成本和使用方式報表是 AWS 建議的方式，來收集和處理 AWS 成本。 如需詳細資訊，請參閱 < [AWS 成本和使用量報告](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)文件文章。

使用**報表**計費與成本管理主控台，在 AWS 中進行下列步驟中建立成本和使用方式報表 頁面。

1. 登入 AWS 管理主控台，並開啟 [計費與成本管理主控台] 中的 https://console.aws.amazon.com/billing。
2. 在 [導覽] 窗格中，按一下**報表**。
3. 按一下 **建立報表**。
4. 針對**報表名稱**，輸入報表的名稱。
5. 針對**時間單位**，選擇**每小時**。
6. 針對**Include**，在報表中新增每個資源的識別碼，然後選取**資源識別碼**。
7. 針對**啟用支援**，沒有選取項目是必要。
8. 針對**資料重新整理設定**，選取**自動重新整理您的成本&amp;使用量報告時使用的前幾個月偵測費用關閉帳單**。
9. 单击“下一步”。
10. 針對**Amazon S3 貯體**，輸入您想要傳遞給報表的 Amazon S3 貯體名稱，然後按一下**確認**。 值區必須是有效的適當權限。 如需有關如何將權限新增到貯體的詳細資訊，請參閱[設定的值區和物件的存取權限](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html)。
11. 針對**報表路徑前置詞**，輸入您想要加入至報表的名稱的報表路徑前置詞。
12. 針對**壓縮**，選取**GZIP**。
13. 单击“下一步”。
14. 在檢閱過您的報表，請按一下 設定之後**檢閱並完成**。
    附註**報表名稱**。 您將在稍後步驟中使用它。

可能需要最多 24 小時以開始將報表傳遞給您的 Amazon S3 貯體的 AWS。 傳遞啟動之後，AWS 更新 AWS 成本和使用方式報表檔一天至少一次。 您可以繼續設定您的 AWS 環境，而不需等待傳遞給啟動。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中建立的角色和原則

Azure 成本管理存取成本和使用方式報表所在的位置數次一天的 S3 貯體。 成本的管理需要認證，以檢查有新的資料存取。 若要允許存取成本管理的 AWS 中建立的角色和原則。

若要啟用角色型存取權的 Azure 成本管理的 AWS 帳戶，在 AWS 主控台中建立的角色。 您必須能夠_角色 ARN_並_外部識別碼_從 AWS 主控台。 稍後，您使用它們在 建立在 Azure 成本管理中的 AWS 連接器頁面。

使用 [建立新的角色] 精靈：

1. 登入 AWS 主控台，然後選取**Services**。
2. 在服務清單中，選取**IAM**。
3. 選取 **角色**，然後按一下**Create Role**。
4. 在下一步 頁面中，選取**另一個 AWS 帳戶**。
5. 在 **帳戶識別碼**，輸入_432263259397_。
6. 在 **選項**，選取**需要外部 ID （最佳做法是第三方會假設此角色）**。
7. 在 **外部識別碼**，輸入外部識別碼。 外部識別碼是 AWS 角色和 Azure 成本管理之間的共用的密碼。 相同的外部識別碼也會在成本管理中的 [新的連接器] 頁面中。 例如，外部識別碼類似_Companyname1234567890123_。
    不會變更選取項目，如**要求 MFA**。 它應該維持已清除項目。
8. 按一下 **[下一步權限]**。
9. 按一下 [Create policy] \(建立原則\)。 新的瀏覽器索引標籤會開啟您用來建立新的原則。
10. 按一下 **選擇服務**。

設定成本和使用量 報表的權限：

1. 型別**成本和使用量報告**。
2. 選取 **存取層級**，**讀取** > **DescribeReportDefinitions**。 這可讓成本管理可讓您讀取什麼 CUR 報告定義，並判斷它們是否相符的報表定義的必要條件。
3. 按一下 **新增額外的權限**。

設定您的 S3 貯體與物件權限：

1. 按一下 **選擇服務**。
2. 型別_S3_。
3. 選取 **存取層級**，**清單** > **ListBucket**。 此動作會取得 S3 貯體中的物件清單。
4. 選取 **存取層級**，**讀取** > **GetObject**。 這個動作可讓計費檔案下載。
5. 選取 **資源**。
6. 選取 **貯體 – 新增 ARN**。
7. 在 **貯體名稱**，輸入用來儲存目前檔案的貯體。
8. 選取 **物件 – 新增 ARN**。
9. 在 **貯體名稱**，輸入用來儲存目前檔案的貯體。
10. 在 **物件名稱**，選取**任何**。
11. 按一下 **新增額外的權限**。

設定成本 Explorer 權限：

1. 按一下 **選擇服務**。
2. 型別_成本 Explorer 服務_。
3. 選取 **成本 Explorer 服務的所有動作 (ce:\*)**。 此動作會驗證集合正確。
4. 按一下 **新增額外的權限**。

新增組織的權限：

1. 型別**組織**。
2. 選取 **存取層級，清單** > **ListAccounts**。 此動作取得的帳戶名稱。
3. 在 **檢閱原則**，輸入新原則的名稱。 請確定您已輸入正確的資訊，然後按一下核取**建立原則**。
4. 返回前一個索引標籤，並重新整理瀏覽器的網頁。 在 [搜尋] 列中，搜尋您的新原則。
5. 選取 **下一步： 檢閱**。
6. 輸入新角色的名稱。 請確定您已輸入正確的資訊，然後按一下核取**Create Role**。
    附註**角色 ARN**並**外部識別碼**建立角色時，在前述步驟中使用。 您稍後將使用這些設定的 Azure 成本管理連接器時。

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

您可以使用下列資訊來建立新的 AWS 連接器並開始監視您的 AWS 成本。

1. 登入 Azure 入口網站，網址 https://portal.azure.com。
2. 瀏覽至**成本管理 + 計費** > **成本管理**。
3. 底下**設定**，按一下**雲端連接器 （預覽）**。  
    ![顯示雲端連接器 （預覽設定） 的範例](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. 按一下  **+ 新增**頂端的頁面，即可建立新的連接器。
5. 在 [建立 AWS 連線程式] 頁面中，輸入**顯示名稱**命名您的連接器。  
    ![建立 AWS 連線程式 頁面的範例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. （選擇性） 選取預設的管理群組。 它會儲存所有探索到連結的帳戶。 您可以將它設定更新版本。
7. 底下**計費**區段中，選取**自動收費在正式運作的 1%** 如果您想要預覽到期時，確保持續的作業。 如果您選取 [自動] 選項時，您必須選取計費**訂用帳戶**。
8. 請輸入**角色 ARN**。 它是設定在 AWS 中的角色時，您所使用的值。
9. 請輸入**外部識別碼**。 它是設定在 AWS 中的角色時，您所使用的值。
10. 請輸入**報表名稱**您在 AWS 中建立。
11. 按一下 **下一步**，然後按一下**建立**。

可能需要幾小時內的新 AWS 範圍、 AWS 彙總帳戶和 AWS 連結的帳戶和其成本資料才會出現。

建立連接器之後，我們建議您將存取控制指派給連接器。 要指派權限給使用者的新探索到的範圍：帳戶和 AWS，AWS 彙總連結的帳戶。 建立連接器的使用者是連接器、 合併的帳戶和所有連結的帳戶擁有者。

將連接器權限指派給使用者之後就會進行探索, 不到現有的 AWS 範圍中指派權限。 相反地，只有新連結的帳戶會指派權限。

## <a name="additional-steps"></a>其他步驟

- [設定管理群組](../governance/management-groups/index.md#initial-setup-of-management-groups)，如果您還沒有這麼做。
- 請檢查新的範圍會加入至您的範圍選擇器。 別忘了按一下**重新整理**若要檢視最新的資料。
- 在 [雲端連接器] 頁面中，選取您的連接器，然後按一下**移至帳單帳戶**將連結的帳戶指派給管理群組。

## <a name="manage-cloud-connectors"></a>管理雲端連接器

當您選取連接器，以在雲端的 [連接器] 頁面上時，您可以：

- 按一下 **移至帳單帳戶**檢視 AWS 合併的帳戶資訊。
- 按一下 **存取控制**管理連接器角色指派。
- 按一下 **編輯**更新連接器。 出現在 角色 ARN，您無法變更 AWS 帳戶號碼。 不過，您可以建立新的連接器。
- 按一下 **確認**重新執行驗證測試，藉此確定成本管理可以收集使用的連接器設定的資料。

![範例顯示建立 AWS 連接器清單](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Azure 的管理群組的角色

若要建立一個單一的位置，來檢視跨雲端提供者資訊，您需要將您的 Azure 訂用帳戶和連結的 AWS 帳戶放在相同的管理群組中。 如果您尚未使用管理群組設定 Azure 環境，請參閱[初始的管理群組安裝](../governance/management-groups/index.md#initial-setup-of-management-groups)。

如果您想要不同的成本，您可以建立包含只是連結的 AWS 帳戶的管理群組。

## <a name="aws-consolidated-account"></a>AWS 合併的帳戶

AWS 合併的帳戶用來將計費和付款的多個的 AWS 帳戶合併。 您的 AWS 彙總帳戶也會擔任 AWS 連結的帳戶。

![AWS 合併的帳戶詳細資料範例](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

從頁面上，您可以：

- 按一下 **更新**大量更新連結的 AWS 帳戶與相關聯的管理群組。
- 按一下 **存取控制**設定範圍的角色指派。

### <a name="aws-consolidated-account-permissions"></a>AWS 合併的帳戶權限

根據預設，AWS 會合併建立後，根據 AWS 連接器權限已設定的權限的帳戶。 連接器的建立者是擁有者。

您可以使用彙總的 AWS 帳戶的存取層級頁，以管理存取層級。 不過，權限的 AWS 彙總帳戶不會繼承 AWS 連結的帳戶。

## <a name="aws-linked-account"></a>AWS 連結帳戶

AWS 連結的帳戶是建立及管理 AWS 資源為目標的位置。 連結的帳戶也可做為安全性界限。

您可以從這個頁面：

- 按一下 **更新**更新連結的 AWS 帳戶關聯的管理群組。
- 按一下 **存取控制**設定範圍的角色指派。

![[連結的 AWS 帳戶] 頁面的範例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>AWS 連結的帳戶權限

根據預設，設定 AWS 連結的帳戶權限建立後，根據 AWS 連接器權限。 連接器的建立者是擁有者。 您可以使用連結的 AWS 帳戶的存取層級頁，以管理存取層級。 AWS 連結的帳戶不會繼承 AWS 整合帳戶的權限。

AWS 連結帳戶一律會繼承從其所屬的管理群組的權限。

## <a name="next-steps"></a>後續步驟

- 現在，您已安裝並設定 AWS 成本和使用方式報表整合，請繼續進行[管理的 AWS 成本和使用量](aws-integration-manage.md)。
- 如果您不熟悉成本分析，請參閱[探索及分析成本，以及成本分析](quick-acm-cost-analysis.md)快速入門。
- 如果您不熟悉 Azure 中的預算，請參閱[建立和管理 Azure 的預算](tutorial-acm-create-budgets.md)教學課程。
