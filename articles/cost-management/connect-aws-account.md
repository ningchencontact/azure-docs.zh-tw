---
title: 將 Amazon Web Services 帳戶連線到 Azure 成本管理 | Microsoft Docs
description: 連線 Amazon Web Services 帳戶以在「成本管理」報表中檢視成本和使用方式資料。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>連線 Amazon Web Services 帳戶

您有兩個選項可以將您的 Amazon Web Services (AWS) 帳戶連線到 Azure 成本管理。 您可以與 IAM 角色或唯讀 IAM 使用者帳戶連線。 建議項目是 IAM 角色，因為它可以讓您將具有已定義權限的存取權委派給信任的實體。 IAM 角色不需要您共用長期存取金鑰。 將 AWS 帳戶連線到成本管理之後，成本和使用方式資料可以在「成本管理」報表中取得。 這份文件會引導您進行這兩個選項。

如需 AWS IAM 身分識別的詳細資訊，請參閱[身分識別 (使用者、群組和角色)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)。

## <a name="aws-role-based-access"></a>AWS 角色型存取

下列各節會引導您完成建立唯讀 IAM 角色以提供成本管理的存取權。

### <a name="get-your-cost-management-account-external-id"></a>取得您的成本管理帳戶外部 ID

第一個步驟是從 Azure 成本管理入口網站取得唯一的連線複雜密碼。 它在 AWS 中用來作為**外部 ID**。

1. 從 Azure 入口網站開啟 Cloudyn 入口網站，或瀏覽至 [https://azure.cloudyn.com](https://azure.cloudyn.com) 並登入。
2. 按一下 [設定] (齒輪圖示)，然後選取 [雲端帳戶]。
3. 在 [帳戶管理] 中，選取 [AWS 帳戶] 索引標籤，然後按一下 [新增 +]。
4. 在 [新增 AWS 帳戶] 對話方塊中，複製**外部 ID**，並且儲存這個值以供下一節的 AWS 角色建立步驟使用。 在下圖中，範例 ID 是 _Cloudyn_。 您的 ID 不同。  
    ![外部 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>新增 AWS 唯讀角色型存取

1. 登入位於 https://console.aws.amazon.com/iam/home 的 AWS 主控台，然後選取 [角色]。
2. 按一下 [建立角色]，然後選取 [另一個 AWS 帳戶]。
3. 在 [帳戶 ID] 欄位中貼上身分識別 `432263259397`。 此帳戶 ID 是您必須使用的成本管理資料收集器帳戶。
4. 在 [選項] 旁邊，選取 [需要外部 ID]，然後在 [外部 ID] 欄位中貼上先前複製的值，然後按 [下一步：權限]。  
    ![建立角色](./media/connect-aws-account/create-role01.png)
5. 在 [附加權限原則] 底下的 [原則類型] 篩選方塊搜尋中，輸入 `ReadOnlyAccess`，選取 [ReadOnlyAccess]，然後按 [下一步：檢閱]。  
    ![唯讀存取](./media/connect-aws-account/readonlyaccess.png)
6. 在 [檢閱] 分頁中，確定您的選擇正確無誤，然後輸入**角色名稱**。 例如，*Azure-Cost-Mgt*。輸入**角色描述**。 例如，_Azure 成本管理的角色指派_，然後按一下 [建立角色]。
7. 在 [角色] 清單中，按一下您建立的角色，然後從 [摘要] 分頁中複製 **角色 ARN** 值。 稍後當您在 Azure 成本管理中註冊設定時，使用角色 ARN 值。  
    ![角色 ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>在成本管理中設定 AWS IAM 角色存取

1. 從 Azure 入口網站開啟 Cloudyn 入口網站，或瀏覽至 https://azure.cloudyn.com/ 並登入。
2. 按一下 [設定] (齒輪圖示)，然後選取 [雲端帳戶]。
3. 在 [帳戶管理] 中，選取 [AWS 帳戶] 索引標籤，然後按一下 [新增 +]。
4. 在 [帳戶名稱] 中，輸入帳戶的名稱。
5. 在 [存取類型] 旁邊，選取 [IAM 角色]。
6. 在 [角色 ARN] 欄位中，貼上您先前複製的值，然後按一下 [儲存]。  
    ![AWS 帳戶狀態](./media/connect-aws-account/aws-account-status01.png)

您的 AWS 帳戶會出現在帳戶清單中。 列出的**擁有者 ID** 與您的角色 ARN 值相符。 您的**帳戶狀態**應該有一個綠色核取符號。

成本管理會開始收集資料並填入報表。 不過，某些最佳化報表可能需要幾天的資料才能產生精確的建議。

## <a name="aws-user-based-access"></a>AWS 以使用者為基礎的存取

下列各節會引導您完成建立唯讀使用者以提供成本管理的存取權。

### <a name="add-aws-read-only-user-based-access"></a>新增 AWS 唯讀以使用者為基礎的存取

1. 登入位於 https://console.aws.amazon.com/iam/home 的 AWS 主控台，然後選取 [使用者]。
2. 按一下 [新增使用者] 。
3. 在 [使用者名稱] 欄位中，輸入使用者名稱。
4. 針對 [存取類型]，選取 [以程式設計方式存取]，然後按 [下一步：權限]。  
    ![新增使用者](./media/connect-aws-account/add-user01.png)
5. 針對權限，選取 [直接附加現有的原則] 按鈕。
6. 在 [附加權限原則] 底下的 [原則類型] 篩選方塊搜尋中，輸入 `ReadOnlyAccess`，選取 [ReadOnlyAccess]，然後按 [下一步：檢閱]。  
    ![設定使用者的權限](./media/connect-aws-account/set-permission-for-user.png)
7. 在 [檢閱] 分頁中，確定您的選擇正確無誤，然後按一下 [建立使用者]。
8. 在 [完成] 分頁中，會顯示您的存取金鑰 ID 和密碼存取金鑰。 您使用此資訊在成本管理中設定註冊。
9. 按一下 [下載 .csv] 以將 credentials.csv 檔案儲存到安全的位置。  
    ![下載認證](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>在成本管理中設定 AWS IAM 以使用者為基礎的存取

1. 從 Azure 入口網站開啟 Cloudyn 入口網站，或瀏覽至 https://azure.cloudyn.com/ 並登入。
2. 按一下 [設定] (齒輪圖示)，然後選取 [雲端帳戶]。
3. 在 [帳戶管理] 中，選取 [AWS 帳戶] 索引標籤，然後按一下 [新增 +]。
4. 針對 [帳戶名稱]，輸入帳戶名稱。
5. 在 [存取類型] 旁邊，選取 [IAM 使用者]。
6. 在 [存取金鑰] 中，貼上來自 credentials.csv 檔案的**存取金鑰 ID** 值。
7. 在 [秘密金鑰] 中，貼上來自 credentials.csv 檔案的**秘密存取金鑰**值，然後按一下 [儲存]。  
    ![AWS 使用者帳戶狀態](./media/connect-aws-account/aws-user-account-status.png)

您的 AWS 帳戶會出現在帳戶清單中。 您的**帳戶狀態**應該有一個綠色核取符號。

成本管理會開始收集資料並填入報表。 不過，某些最佳化報表可能需要幾天的資料才能產生精確的建議。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 成本管理，請繼續進行「成本管理」的[檢閱使用量和成本](tutorial-review-usage.md)教學課程。
