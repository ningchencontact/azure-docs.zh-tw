---
title: 在 Marketo 中設定潛在客戶管理 |Azure Marketplace
description: 為 Azure marketplace 客戶設定 Marketo 的潛在客戶管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 9470a593f5db564689a0b26601a201874c215aa2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902087"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中設定潛在客戶管理

本文說明如何設定您的 Marketo CRM 系統, 以處理來自 marketplace 供應專案的銷售潛在客戶。

## <a name="set-up-your-marketo-crm-system"></a>設定您的 Marketo CRM 系統

1. 登入 Marketo。
2. 選取 [Design Studio] \(設計工作室\)。
    ![Marketo [Design Studio] \(設計工作室\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  選取 [New Form] \(新增表單\)。
    ![Marketo [New Form] \(新增表單\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  填入 [New Form] \(新增表單\) 中的必要欄位，然後選取 [Create] \(建立\)。
    ![Marketo 建立新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  在 [Field Details] \(欄位詳細資料\) 上，選取 [Finish] \(完成\)。
    ![Marketo 完成表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  核准並關閉。

7. 在 [ *MarketplaceLeadBacked* ] 索引標籤上, 選取 [**內嵌程式碼**]。 

    ![內嵌程式碼](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 複製 [內嵌程式碼] 表單中所顯示之下欄欄位的值。 您將會使用這些值來設定您的供應專案, 以便在下一個步驟中收到潛在客戶。 使用下一個範例作為從 Marketo [Embed Code] \(內嵌程式碼\) 範例取得所需識別碼的指南。

    - 伺服器識別碼 = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 表單識別碼 = **1179**

    **另一種找出這些值的方式**

    - 伺服器識別碼可在 Marketo 實例的 URL 中找到, 例如 "`serverID.marketo.com`"。
    - 前往 [Munchkin 帳戶識別碼] 欄位中的 [系統管理員] > [Munchkin] 功能表, 或從 Marketo REST API 主機子域的第一個部分, 取得訂用帳戶的`https://{Munchkin ID}.mktorest.com`Munching 識別碼:。
    - [表單識別碼] 是您在步驟7中建立的內嵌程式碼表單識別碼, 以從我們的 marketplace 路由潛在客戶。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>設定您的供應專案以將潛在客戶傳送至 Marketo

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時, 請遵循下列步驟: 

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
1. 選取 [潛在客戶管理] 區段下的 **[連接]** 。 

    ![潛在客戶管理-Connect](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在 [連線詳細資料] 快顯視窗中, 針對 [潛在客戶目的地] 選取 [ **Marketo** ]。

    ![選擇潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 提供 [**伺服器識別碼**]、[ **Munching 帳戶識別碼**] 和 [**表單識別碼**]。

    >[!Note]
    >您必須先完成供應專案的其餘部分設定並加以發佈, 才能接收供應專案的潛在客戶。 

