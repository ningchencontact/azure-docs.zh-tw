---
title: 設定在 Marketo 中的潛在客戶管理 |Azure Marketplace
description: 設定適用於 Marketo 的潛在客戶管理的 Azure marketplace 客戶。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: d749a92a1837bad053dc586477bfc27fc65299fa
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935219"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中設定潛在客戶管理

此文章說明如何設定 Marketo 以處理 Microsoft 業務潛在客戶。

1. 登入 Marketo。
2. 選取 [Design Studio] \(設計工作室\)。
    ![Marketo [Design Studio] \(設計工作室\)](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  選取 [New Form] \(新增表單\)。
    ![Marketo [New Form] \(新增表單\)](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  填入 [New Form] \(新增表單\) 中的必要欄位，然後選取 [Create] \(建立\)。
    ![Marketo 建立新表單](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  在 [Field Details] \(欄位詳細資料\) 上，選取 [Finish] \(完成\)。
    ![Marketo 完成表單](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  核准並關閉。

6.  在 [MarketplaceLeadBacked] 索引標籤上，選取 [Embed Code] \(內嵌程式碼\)。
    ![Marketo [Embed Code] \(內嵌程式碼\) 選項](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. 複製顯示於 [Embed Code] \(內嵌程式碼\) 中的值，以設定 Cloud Partner 入口網站上 Marketo 欄位中的 [伺服器識別碼]、[Munchkin 識別碼]，以及 [表單識別碼]。

使用下一個範例作為從 Marketo [Embed Code] \(內嵌程式碼\) 範例取得所需識別碼的指南。

- 伺服器識別碼 = **ys12**
- Munchkin 識別碼 = **123-PQR-789**
- 表單識別碼 = **1179**\
