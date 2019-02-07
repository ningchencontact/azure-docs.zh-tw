---
title: 更新 Azure App Service on Azure Stack | Microsoft Docs
description: 適用於更新 Azure App Service on Azure Stack 的詳細指導方針
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: anwestg
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: a784fd1b4b03fa2f6574926481351046a65d7611
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769268"
---
# <a name="update-azure-app-service-on-azure-stack"></a>更新 Azure App Service on Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

> [!IMPORTANT]  
> 在部署 Azure App Service 1.4 之前，請先將 1809 更新 (或更新版本) 套用到您的 Azure Stack 整合式系統，或部署最新的 Azure Stack 開發套件。 在更新資源提供者之前，請先檢閱版本資訊，以了解有哪些新功能、修正，以及任何可能對部署造成影響的已知問題。

您可以遵循本文章中的指示，將部署在連線到網際網路之 Azure Stack 環境中的 [App Service 資源提供者](azure-stack-app-service-overview.md)進行升級。

> [!IMPORTANT]  
> 在執行升級之前，請確定您已完成 [Azure App Service on Azure Stack 資源提供者的部署](azure-stack-app-service-deploy.md)

## <a name="run-the-app-service-resource-provider-installer"></a>執行 App Service 資源提供者安裝程式

在此過程中，升級將會：

* 偵測先前部署的 App Service
* 準備要部署之所有 OSS 程式庫的所有更新套件和新版本
* 上傳至儲存體
* 升級所有的 App Service 角色 (控制器、管理、前端、發行者和背景工作角色)
* 更新 App Service 擴展集定義
* 更新 App Service 資源提供者資訊清單

> [!IMPORTANT]
> App Service 安裝程式必須在可觸達「Azure Stack 系統管理員 Azure Resource Manager 端點」的機器上執行。
>
>

若要升級您的 App Service on Azure Stack 部署，請遵循下列步驟：

1. 下載 [App Service 安裝程式](https://aka.ms/appsvcupdate4installer)

2. 以系統管理員身分執行 appservice.exe

    ![App Service 安裝程式][1]

3. 按一下 [部署 App Service 或升級為最新版本。]

4. 檢閱並接受 Microsoft 軟體授權條款，然後按 [下一步]。

5. 檢閱並接受協力廠商授權條款，然後按 [下一步]。

6. 確定 Azure Stack Azure Resource Manager 端點和 Active Directory 租用戶資訊是正確的。 如果您在 Azure Stack 開發套件部署期間使用了預設設定，在這裡可以接受預設值。 不過，如果部署 Azure Stack 時自訂了選項，則必須編輯此視窗中的值。 例如，如果您使用網域尾碼「mycloud.com」，則您的 Azure Stack Azure Resource Manager 端點必須變更為「management.region.mycloud.com」。 確認您的資訊之後，按 [下一步]。

    ![Azure Stack 雲端資訊][2]

7. 在下一個頁面上：

   1. 按一下 [Azure Stack 訂用帳戶] 方塊旁邊的 [連線] 按鈕。
        * 如果您使用 Azure Active Directory (Azure AD)，請輸入部署 Azure Stack 時所提供的 Azure AD 管理員帳戶和密碼。 按一下 [登入]。
        * 如果您使用 Active Directory 同盟服務 (AD FS)，請提供您的管理帳戶。 例如：*cloudadmin@azurestack.local*。 輸入您的密碼，然後按一下 [登入]。
   2. 在 [Azure Stack 訂用帳戶] 方塊中，選取 [預設提供者訂用帳戶]。
   3. 在 [Azure Stack 位置] 方塊中，選取對應到您要部署之區域的位置。 例如，如果要部署至 Azure Stack 開發套件，請選取 [本機]。
   4. 如果偵測到現有的 App Service 部署，就會將資源群組和儲存體帳戶填入並呈現灰色。
   5. 按 [下一步] 以檢閱升級摘要。

    ![偵測到 App Service 安裝][3]

8. 在摘要頁面上：
   1. 確認您所做的選擇。 若要進行變更，請使用 [上一步] 按鈕來瀏覽先前的頁面。
   2. 如果設定均正確，請選取核取方塊。
   3. 若要開始升級，請按 [下一步]。

       ![App Service 升級摘要][4]

9. 升級進度頁面：
    1. 追蹤升級進度。 App Service on Azure Stack 升級的持續時間會依所部署的角色執行個體數目而有所不同。
    2. 升級成功完成之後，按一下 [結束]。

        ![App Service 升級進度][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>後續步驟

您也可以嘗試其他[平台即服務 (PaaS) 服務](azure-stack-tools-paas-services.md)。

* [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md)
