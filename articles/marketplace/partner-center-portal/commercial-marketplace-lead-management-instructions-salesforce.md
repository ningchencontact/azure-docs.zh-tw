---
title: 設定 Salesforce 的潛在客戶管理 |Azure Marketplace
description: 為 Azure Marketplace 客戶設定 Salesforce 上的潛在客戶管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: d941a7902dd374a931147bdaa8f015c46268ee8a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901983"
---
# <a name="configure-lead-management-for-salesforce"></a>針對 Salesforce 設定潛在客戶管理

本文說明如何設定您的 Salesforce 系統, 以處理來自 marketplace 供應專案的銷售潛在客戶。

## <a name="set-up-your-salesforce-system"></a>設定您的 Salesforce 系統

1. 登入 Salesforce。
2. 如果您使用的是 Salesforce 光源體驗。
    1. 從 Salesforce 首頁選取 [**安裝**]。
    ![Salesforce 設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 從 [設定] 頁面中, 流覽至 [**平臺工具-> 功能設定-> 行銷]-> [Web 到潛在客戶**]。
    ![Salesforce Web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. 如果您使用的是 Salesforce 傳統體驗:
    1. 從 Salesforce 首頁選取 [**安裝**]。
    ![Salesforce 傳統設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 從 [安裝] 頁面, 透過左側導覽導覽至**組建-> 自訂 > 的潛在客戶-> 的 Web 導向**。
    ![Salesforce 傳統的 web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

無論您使用的是哪個 Salesforce 體驗, 其餘的指示都相同。

4. 在 [**網路到潛在客戶設定] 頁面**上, 選取 [**建立 Web 到潛在客戶表單**] 按鈕。
5. 在 [Web 到潛在客戶設定] 中，選取 [建立 Web 到潛在客戶表單]。
    ![Salesforce-網路對導向設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 在 [**建立 Web 到潛在客戶] 表單**上, 確定`the Include reCAPTCHA in HTML`未核取 [設定], 然後選取 [**產生**]。 
    ![Salesforce-建立 Web 到潛在客戶表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 您會看到一些 HTML 文字。 搜尋文字 "oid", 並從 HTML 文字複製**oid 值**(僅限在引號之間的文字) 並加以儲存。 您會在發佈入口網站的 [**組織識別碼**] 欄位中貼上此值。
    ![Salesforce-建立 Web 到潛在客戶表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. 選取 [**完成**]。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>設定您的供應專案以將潛在客戶傳送至 Salesforce

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時, 請遵循下列步驟:

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
1. 選取 [潛在客戶管理] 區段下的 **[連接]** 。
    ![潛在客戶管理-Connect](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在 [連線詳細資料] 快顯視窗中, 選取 [ **Salesforce** ] 作為 [**潛在客戶目的地**], 並將您在先前步驟中所建立的 [ `oid`從 web 到潛在客戶] 表單貼入 [**組織識別碼**] 欄位。

1. 選取 [ **儲存**]。 

    >[!Note]
    >您必須先完成供應專案的其餘部分設定並加以發佈, 才能接收供應專案的潛在客戶。

    ![連接詳細資料-選擇潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![連接詳細資料-選擇潛在客戶目的地](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)