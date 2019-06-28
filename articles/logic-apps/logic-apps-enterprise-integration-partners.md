---
title: 加入交易夥伴進行 B2B 整合-Azure Logic Apps
description: 建立交易夥伴整合帳戶中的，以搭配 Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 681f16132c1de2ec5f3b27f80633d32879b0746c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330183"
---
# <a name="add-trading-partners-to-integration-accounts-for-azure-logic-apps"></a>加入整合帳戶中的交易夥伴，適用於 Azure Logic Apps

在  [Azure Logic Apps](../logic-apps/logic-apps-overview.md)，您可以使用來建立自動化的企業對企業 (B2B) 整合工作流程[整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)邏輯應用程式。 若要代表您的組織和其他項目，您會建立，並將交易夥伴為成品新增到整合帳戶。 合作夥伴是參與 B2B 交易，並與彼此交換訊息的實體。

之前建立這些合作夥伴，請確定來討論及分享資訊與夥伴如何識別及驗證另所傳送的訊息。 您同意這些詳細資料之後，您即可建立整合帳戶中的夥伴。

## <a name="partner-roles-in-integration-accounts"></a>整合帳戶中的夥伴角色

若要定義與夥伴交換之訊息的相關詳細資料，建立並新增[協議](../logic-apps/logic-apps-enterprise-integration-agreements.md)為整合帳戶成品。 合約需要整合帳戶中的至少兩個夥伴。 您的組織不一定*主控夥伴*協議中。 與您的組織交換訊息的組織*來賓夥伴*。 來賓夥伴可以是另一家公司或甚至是您組織中的部門。 在您新增這些合作夥伴之後，您可以建立合約。

在協議中，您可以指定處理內送和外寄訊息從主控夥伴觀點來看的詳細資料。 內送訊息，如**接收設定**指定主控夥伴如何接收訊息來自來賓夥伴協議中。 外寄訊息，如**傳送設定**指定主控夥伴如何將訊息傳送至來賓夥伴。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)來儲存您的夥伴、 合約及其他 B2B 成品。 此整合帳戶必須與您 Azure 訂用帳戶相關聯。

## <a name="create-partner"></a>建立合作夥伴

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在主要 Azure 功能表上，選取 [所有服務]  。 在 [搜尋] 方塊中，輸入 「 整合 」，然後選取**整合帳戶**。

   ![選取 [整合帳戶]](./media/logic-apps-enterprise-integration-partners/find-integration-accounts.png)

1. 在 [整合帳戶]  底下，選取要將合作夥伴新增到其中的整合帳戶。

   ![選取企業整合帳戶](./media/logic-apps-enterprise-integration-partners/select-integration-account.png)

1. 選擇 [合作夥伴]  圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners.png)

1. 在 [合作夥伴]  底下，選擇 [新增]  。 底下**新增協力廠商**，提供交易夥伴的詳細資料，如下表所述。

   ![選擇 [新增]，並提供合作夥伴詳細資料](./media/logic-apps-enterprise-integration-partners/add-partners.png)

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 夥伴的名稱 |
   | **Qualifier** | 是 | 提供唯一的商務識別的組織，例如，驗證內文**D-U-N-S (Dun & Bradstreet)** 。 <p>夥伴可以選擇使用者相互定義的商務識別。 針對這些案例中，選取**使用者相互定義**edifact 或**使用者相互定義 (X12)** 適用於 X12。 <p>RosettaNet 中，選取只**DUNS**，這是標準。 |
   | **值** | 是 | 值，這個值會識別您的 logic apps 接收的文件。 <p>RosettaNet，這個值必須對應至的 DUNS 編號九位數的鄧白氏編號。 |
   ||||

   > [!NOTE]
   > 適用於使用 RosettaNet 的合作夥伴，您可以指定額外的資訊，藉由先建立這些合作夥伴，然後[進行編輯之後](#edit-partner)。

1. 完成時，選擇 [確定]  。

   您的新合作夥伴現在會出現在**合作夥伴**清單。 此外，**合作夥伴**磚會更新目前的夥伴數目。

   ![新的夥伴](./media/logic-apps-enterprise-integration-partners/new-partner.png)

<a name="edit-partner"></a>

## <a name="edit-partner"></a>編輯合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。
選擇 [合作夥伴]  圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/edit.png)

1. 底下**合作夥伴**，選取您想要編輯，然後選擇的合作夥伴**編輯**。 底下**編輯**，進行變更。

   ![進行變更並儲存](./media/logic-apps-enterprise-integration-partners/edit-partner.png)

   For RosettaNet 下**RosettaNet 交易夥伴屬性**，您可以指定這項額外資訊：

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **夥伴分類** | 否 | 合作夥伴的組織類型 |
   | **供應鏈代碼** | 否 | 合作夥伴的供應鏈代碼，例如，"Information Technology"或"Electronic Components" |
   | **連絡人名稱** | 否 | 交易夥伴的連絡人名稱 |
   | **電子郵件** | 否 | 合作夥伴的電子郵件地址 |
   | **Fax** | 否 | 交易夥伴的傳真號碼 |
   | **電話** | 否 | 交易夥伴的電話號碼 |
   ||||

1. 當您完成時，選擇**確定**以儲存變更。

## <a name="delete-partner"></a>刪除合作夥伴

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取整合帳戶。 選擇 [合作夥伴]  圖格。

   ![選擇「合作夥伴」圖格](./media/logic-apps-enterprise-integration-partners/choose-partners-to-delete.png)

1. 在 [合作夥伴]  底下，選取您要刪除的合作夥伴。 選擇 [刪除]  。

   ![刪除合作夥伴](./media/logic-apps-enterprise-integration-partners/delete-partner.png)

## <a name="next-steps"></a>後續步驟

* 深入了解[協議](../logic-apps/logic-apps-enterprise-integration-agreements.md)