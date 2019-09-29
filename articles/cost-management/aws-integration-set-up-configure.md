---
title: 安裝和設定 AWS 成本和使用方式報表與 Azure 成本管理的整合
description: 本文會逐步引導您設定和設定 AWS 成本和使用方式報告與 Azure 成本管理的整合。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: deb13b833707849bcbce8bcae7b05aeb5e0bce3b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338868"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>設定 AWS 成本和使用量報告整合

透過 Amazon Web Services （AWS）成本和使用量報告（上）整合，您可以在 Azure 成本管理中監視並控制 AWS 支出。 整合允許 Azure 入口網站中的單一位置，您可以在其中監視和控制 Azure 和 AWS 的費用。 本文說明如何設定整合並加以設定，讓您可以使用 Azure 成本管理功能來分析成本及查看預算。

成本管理會使用您的 AWS 存取認證來取得報告定義和下載報告 GZIP CSV 檔案，以處理儲存在 S3 bucket 中的 AWS 成本和使用方式報表。

## <a name="create-a-cost-and-usage-report-in-aws"></a>在 AWS 中建立成本和使用方式報表

使用成本和使用量報告是收集和處理 AWS 成本的 AWS 建議方式。 如需詳細資訊，請參閱[AWS 成本和使用方式報表](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)檔。

在 AWS 中使用計費和成本管理主控台的 [**成本 & 使用量報告**] 頁面，以使用下列步驟建立成本和使用量報告：

1. 登入 AWS 管理主控台，並開啟 [[計費及成本管理] 主控台](https://console.aws.amazon.com/billing)。
2. 在流覽窗格中，選取 [**成本] & [使用方式報表**]。
3. 選取 [**建立報表**]。
4. 針對 [**報表名稱**]，輸入報表的名稱。
5. 在 [**其他報表詳細資料**] 下，選取 [**包含資源識別碼**]。
6. 針對 [資料重新整理]**設定**，選取您是否想要在完成帳單之後，AWS 將退款、信用額度或支援費用套用至您的帳戶，以 AWS 成本和使用方式報表重新整理。 當報表重新整理時，會將新的報表上傳到 Amazon S3。 我們建議您保留選取的設定。
7. 選取 [下一步]。
8. 針對 [ **S3 bucket**]，選擇 [**設定**]。
9. 在 [設定 S3 Bucket] 對話方塊中，執行下列其中一項工作：
    1. 從下拉式清單中選取現有的值區，然後選擇 **[下一步]** 。
    2. 輸入值區名稱，以及您要建立新 bucket 的區域，然後選擇 **[下一步]** 。
10. 選取 [**我已確認這是正確的原則**]，然後按一下 [**儲存**]。
11. 選擇性針對 [報表路徑前置詞]，輸入您想要在報表名稱前面加上的報表路徑前置詞。
如果您未指定前置詞，預設前置詞就是您為報表指定的名稱。 日期範圍的格式為 @no__t 0。
12. 針對 [**時間單位**]，選擇 [**每小時**]。
13. 針對**報表版本**設定，選擇您是否要讓每個版本的報表覆寫先前的版本，或如果您想要其他新的報表。
14. 針對 [**啟用資料整合**]，不需要任何選取專案。
15. 針對 [**壓縮**]，選取 [ **GZIP**]。
16. 選取 [下一步]。
17. 在您檢查過報表的設定之後，請選取 [**審查並完成**]。

    記下報表名稱。 您將在稍後的步驟中使用它。

最多可能需要24小時的時間，AWS 才會開始將報表傳遞至您的 Amazon S3 bucket。 傳遞開始之後，AWS 一天至少會更新 AWS 成本和使用量報告檔案一次。 您可以繼續設定 AWS 環境，而不需要等待傳遞開始。

## <a name="create-a-role-and-policy-in-aws"></a>在 AWS 中建立角色和原則

Azure 成本管理存取 S3 值區，其中的成本和使用方式報表會一天找到數次。 服務需要存取認證，才能檢查是否有新的資料。 您會在 AWS 中建立角色和原則，以允許成本管理存取它。

若要在成本管理中啟用 AWS 帳戶的以角色為基礎的存取，請在 AWS 主控台中建立角色。 您需要有來自 AWS 主控台的_角色 ARN_和_外部識別碼_。 稍後，您會在成本管理的 [**建立 AWS 連接器**] 頁面上使用這些專案。

使用 [建立新的角色] 嚮導：

1. 登入您的 AWS 主控台，然後選取 [**服務**]。
2. 在服務清單中，選取 [ **IAM**]。
3. 選取 [**角色**]，然後選取 [**建立角色**]。
4. 在下一個頁面上，選取**另一個 AWS 帳戶**。
5. 在 [**帳戶識別碼**] 中，輸入**432263259397**。
6. 在 [**選項**] 中，選取 **[需要外部識別碼（在協力廠商將會採用此角色時的最佳作法）** ]。
7. 在 [**外部識別碼**] 中，輸入外部識別碼。 外部識別碼是 AWS 角色和 Azure 成本管理之間的共用密碼。 在成本管理的 [**新增連接器**] 頁面上也會使用相同的外部識別碼。 例如，外部識別碼類似_Companyname1234567890123_。

    > [!NOTE]
    > 請勿變更 [**需要 MFA**] 的選取專案。 它應該保持已清除狀態。
8. 完成時，選取 [下一步:**權限]** 。
9. 選取 [建立原則]。 隨即開啟新的瀏覽器索引標籤。 這就是您建立原則的地方。
10. 選取 **[選擇服務**]。

設定成本和使用量報告的許可權：

1. 輸入**成本和使用量報告**。
2. 選取**存取層級** > **讀取** > **DescribeReportDefinitions**。 此步驟可讓成本管理讀取目前定義的報表，並判斷它們是否符合報表定義的必要條件。
3. 選取 [**新增其他許可權**]。

設定 S3 bucket 和物件的許可權：

1. 選取 **[選擇服務**]。
2. 輸入**S3**。
3. 選取 [**存取層級** > ]**清單** > **ListBucket**。 此動作會取得 S3 Bucket 中的物件清單。
4. 選取**存取層級** > **讀取** > **GetObject**。 此動作可讓您下載帳單檔案。
5. 選取 [**資源**]。
6. 選取 [ **bucket –新增 ARN**]。
7. 在 [ **Bucket 名稱**] 中，輸入用來儲存當前檔案的 Bucket。
8. 選取 [**物件–新增 ARN**]。
9. 在 [ **Bucket 名稱**] 中，輸入用來儲存當前檔案的 Bucket。
10. 在 [**物件名稱**] 中，選取 [**任何**]。
11. 選取 [**新增其他許可權**]。

設定成本瀏覽器的許可權：

1. 選取 **[選擇服務**]。
2. 輸入**成本瀏覽器服務**。
3. 選取 **[所有成本瀏覽器服務動作] （ce： \*）** 。 此動作會驗證集合是否正確。
4. 選取 [**新增其他許可權**]。

新增 AWS 組織的許可權：

1. 輸入**組織**。
2. 選取 [**存取層級** > ]**清單** > **ListAccounts**。 此動作會取得帳戶的名稱。
3. 在 [**審查原則**] 中，輸入新原則的名稱。 請確認您輸入的是正確的資訊，然後選取 [**建立原則**]。
4. 回到上一個索引標籤，然後重新整理瀏覽器的網頁。 在搜尋列中，搜尋您的新原則。
5. 完成時，選取 下一步: **:** 。
6. 輸入新角色的名稱。 請確認您輸入的是正確的資訊，然後選取 [**建立角色**]。

    請注意，當您建立角色時，會在先前步驟中使用 [角色 ARN] 和 [外部識別碼]。 稍後當您設定 Azure 成本管理連接器時，將會用到它們。

原則 JSON 應如下列範例所示。 將_bucketname_取代為您的 S3 bucket 名稱。

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>在 Azure 中設定新的 AWS 連接器

使用下列資訊來建立 AWS 連接器，並開始監視您的 AWS 成本：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 前往**成本管理 + 帳單** > **成本管理**。
3. 在 [**設定**] 底下，選取 **[雲端連接器（預覽）** ]。  
    ![Example 顯示 [雲端連接器（預覽）] 設定） ](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. 選取頁面頂端的 [ **+ 新增**]，以建立連接器。
5. 在 [**建立 AWS 連接器**] 頁面的 [**顯示名稱**] 中，輸入連接器的名稱。  
    建立 AWS 連接器 @ no__t-1 的頁面 @no__t 0Example
6. （選擇性）選取 [預設管理群組]。 它會儲存所有探索到的連結帳戶。 您可以稍後再設定。
7. 如果您想要確保預覽到期時的持續作業，請在 [**計費**] 區段中選取 [**在公開上市時自動向 1% 收費**]。 如果您選取 [自動] 選項，則必須選取計費訂用帳戶。
8. 針對 [**角色 ARN**]，輸入您在 AWS 中設定角色時所使用的值。
9. 針對 [**外部識別碼**]，輸入您在 AWS 中設定角色時所使用的值。
10. 針對 [**報告名稱**]，輸入您在 AWS 中建立的名稱。
11. 選取 **[下一步]** ，然後選取 [**建立**]。

新的 AWS 範圍、AWS 合併帳戶、AWS 連結的帳戶，以及其成本資料可能需要幾個小時才會出現。

建立連接器之後，建議您將存取控制指派給它。 系統會將許可權指派給使用者，以獲得新探索到的範圍：AWS 合併帳戶和 AWS 連結的帳戶。 建立連接器的使用者是連接器的擁有者、合併的帳戶，以及所有連結的帳戶。

在探索之後將連接器許可權指派給使用者，並不會將許可權指派給現有的 AWS 範圍。 相反地，只有新連結的帳戶會被指派許可權。

## <a name="take-additional-steps"></a>採取其他步驟

- [設定管理群組](../governance/management-groups/overview.md#initial-setup-of-management-groups)（如果您還沒這麼做的話）。
- 檢查是否已將新的範圍新增至您的範圍選取器。 選取 **[** 重新整理] 以查看最新的資料。
- 在 [**雲端連接器**] 頁面上，選取您的連接器，然後選取 [**移至帳單帳戶**]，將連結的帳戶指派給管理群組。

## <a name="manage-cloud-connectors"></a>管理雲端連接器

當您選取 [**雲端連接器**] 頁面上的連接器時，您可以：

- 選取 [**前往帳單帳戶**] 以查看 AWS 合併帳戶的資訊。
- 選取 [**存取控制**] 以管理連接器的角色指派。
- 選取 [**編輯**] 以更新連接器。 您無法變更 AWS 帳戶號碼，因為它出現在角色 ARN 中。 但是，您可以建立新的連接器。
- 選取 [**確認**] 以重新執行驗證測試，以確定成本管理可以使用連接器設定來收集資料。

![已建立 AWS 連接器的範例清單](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>設定 Azure 管理群組

將您的 Azure 訂用帳戶和 AWS 連結的帳戶放在相同的管理群組中，以建立可查看跨雲端提供者資訊的單一位置。 如果您尚未使用管理群組設定 Azure 環境，請參閱[管理群組的初始設定](../governance/management-groups/overview.md#initial-setup-of-management-groups)。

如果您想要區分成本，可以建立一個只保存 AWS 連結帳戶的管理群組。

## <a name="set-up-an-aws-consolidated-account"></a>設定 AWS 合併帳戶

AWS 合併帳戶會合並多個 AWS 帳戶的帳單和付款。 它也會作為 AWS 連結帳戶。

![AWS 合併帳戶的範例詳細資料](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

從頁面上，您可以：

- 選取 [**更新**] 以大量更新 AWS 連結帳戶與管理群組的關聯。
- 選取 [**存取控制**] 以設定範圍的角色指派。

### <a name="permissions-for-an-aws-consolidated-account"></a>AWS 合併帳戶的許可權

根據預設，AWS 合併帳戶的許可權是依據 AWS 連接器許可權，在帳戶建立時設定。 連接器建立者是擁有者。

您可以使用 AWS 合併帳戶的 [**存取層級**] 頁面來管理存取層級。 不過，AWS 連結的帳戶不會繼承 AWS 合併帳戶的許可權。

## <a name="set-up-an-aws-linked-account"></a>設定 AWS 連結帳戶

AWS 連結的帳戶是建立和管理 AWS 資源的位置。 連結的帳戶也可做為安全性界限。

在此頁面中, 您可以:

- 選取 [**更新**]，以更新 AWS 連結帳戶與管理群組的關聯。
- 選取 [**存取控制**] 以設定範圍的角色指派。

![AWS 連結帳戶頁面的範例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>AWS 連結帳戶的許可權

根據預設，AWS 連結帳戶的許可權會在建立時設定，並以 AWS 連接器許可權為基礎。 連接器建立者是擁有者。 您可以使用 AWS 連結帳戶的 [**存取層級**] 頁面來管理存取層級。 AWS 連結的帳戶不會繼承 AWS 合併帳戶的許可權。

AWS 連結的帳戶一律會繼承其所屬管理群組的許可權。

## <a name="next-steps"></a>後續步驟

- 現在您已設定 AWS 成本和使用量報告整合，請繼續[管理 AWS 成本和使用量](aws-integration-manage.md)。
- 如果您不熟悉成本分析，請參閱[使用成本分析快速入門探索和分析成本](quick-acm-cost-analysis.md)。
- 如果您不熟悉 Azure 中的預算，請參閱[建立和管理 azure 預算](tutorial-acm-create-budgets.md)。
