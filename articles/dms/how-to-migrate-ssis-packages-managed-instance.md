---
title: 將 SQL Server Integration Services 封裝移轉至 Azure SQL Database 受控執行個體 |Microsoft Docs
description: 了解如何將 SQL Server Integration Services 封裝移轉至 Azure SQL Database 受控執行個體。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083180"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>將 SQL Server Integration Services 封裝移轉至 Azure SQL Database 受控執行個體
如果您使用 SQL Server Integration Services (SSIS)，並想要從來源到目的地 Azure SQL Database 受控執行個體所裝載的 SSISDB 的 SQL Server 所裝載的 SSISDB 移轉您的 SSIS 專案/套件，您可以使用 Azure 資料庫移轉服務。

如果您使用的 SSIS 的版本早於 2012年或您使用非 SSISDB 封裝存放區類型，再移轉您的 SSIS 專案/套件，您需要將它們轉換使用也可以從 SSMS 啟動 Integration Services 專案轉換精靈。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務 (DMS) 目前不支援 Azure SQL Database 做為目標的移轉目的地。 若要重新部署 SSIS 專案/套件部署到 Azure SQL Database，請參閱文章[到 Azure SQL Database 的重新部署 SQL Server Integration Services 封裝](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)。

在本文中，您將了解：
> [!div class="checklist"]
>
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您需要：

* 若要建立的 Azure 資料庫移轉服務的 Azure 虛擬網路 (VNet)，藉由使用 Azure Resource Manager 部署模型，使用提供給您的內部部署來源伺服器的站對站連線能力[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或是[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 如需詳細資訊，請參閱文章[Azure SQL database 網路拓撲管理使用 Azure 資料庫移轉服務的執行個體移轉]( https://aka.ms/dmsnetworkformi)。 如需建立 VNet 的詳細資訊，請參閱[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/)，特別是快速入門文章，裡面會提供逐步操作詳細資料。
* 若要確保您的 VNet 網路安全性群組規則不會封鎖 Azure 資料庫移轉服務的下列輸入的通訊連接埠：443、53、9354、445、12000。 如需 Azure VNet NSG 流量篩選的詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)。
* 若要設定您[來源資料庫引擎存取的 Windows 防火牆](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)。
* 若要開啟 Windows 防火牆以允許 Azure 資料庫移轉服務存取來源 SQL Server，其預設值是 TCP 通訊埠 1433年。
* 如果您使用動態連接埠執行多個具名 SQL Server 執行個體，您可以啟用 SQL Browser 服務並允許通過防火牆存取 UDP 連接埠 1434，讓 Azure 資料庫移轉服務連線來源伺服器上的具名執行個體。
* 如果您是在來源資料庫前面使用防火牆設備，您可能必須新增防火牆規則，才能讓 Azure 資料庫移轉服務存取用於移轉的來源資料庫，以及透過 SMB 連接埠 445 存取檔案。
* Azure SQL Database 受控執行個體來裝載 SSISDB。 如果您需要建立一個，請依照下列文章中的詳細[建立 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。
* 若要確認用來連接到來源的登入 SQL Server 和目標受控執行個體是 sysadmin 伺服器角色的成員。
* 若要確認，SSIS 會佈建在 Azure Data Factory (ADF) 與目的地 Azure SQL Database 所裝載的 SSISDB 中包含 Azure SSIS Integration Runtime (IR) 受控執行個體 (如本文所述[建立 Azure SSISAzure Data Factory 中的整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime))。

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件

評估來源 SSISDB 不尚未整合至 Database Migration Assistant (DMA)，而您 SSIS 專案/套件將會評估/驗證，他們正在重新部署到 Azure SQL Database 受控執行個體裝載的 SSISDB 的目的地。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>註冊 Microsoft.DataMigration 資源提供者

1. 登入 Azure 入口網站，選取 [所有服務]  ，然後選取 [訂用帳戶]  。

    ![顯示入口網站訂用帳戶](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. 選取您要在其中建立 Azure 資料庫移轉服務執行個體的訂用帳戶，然後選取 [資源提供者]  。

    ![顯示資源提供者](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 搜尋移轉，然後在 [Microsoft.DataMigration]  的右邊，選取 [註冊]  。

    ![註冊資源提供者](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>建立 Azure 資料庫移轉服務執行個體

1. 在 Azure 入口網站中，選取 [+ 建立資源]  ，搜尋 [Azure 資料庫移轉服務]  ，然後從下拉式清單選取 [Azure 資料庫移轉服務]  。

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. 在 [Azure 資料庫移轉服務]  畫面上，選取 [建立]  。

    ![建立 Azure 資料庫移轉服務執行個體](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. 在 [建立移轉服務]  畫面上，指定服務的名稱、訂用帳戶，以及新的或現有的資源群組。

4. 選取您要建立 DMS 執行個體的位置。

5. 選取現有 VNet 或建立一個。

    VNet 會提供 Azure 資料庫移轉服務存取來源 SQL Server 和目標 Azure SQL Database 受控執行個體。

    如需有關如何在 Azure 入口網站中建立 VNet 的詳細資訊，請參閱[使用 Azure 入口網站建立虛擬網路](https://aka.ms/DMSVnet)一文。

    如需其他詳細資訊，請參閱[了解使用 Azure 資料庫移轉服務進行 Azure SQL DB 受控執行個體移轉的網路拓樸](https://aka.ms/dmsnetworkformi)一文。

6. 選取定價層。

    如需成本和定價層的詳細資訊，請參閱[定價分頁](https://aka.ms/dms-pricing)。

    ![建立 DMS 服務](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. 選取 [建立]  以建立服務。

## <a name="create-a-migration-project"></a>建立移轉專案

建立服務執行個體之後，請在 Azure 入口網站中找出該服務，然後建立新的移轉專案。

1. 在 Azure 入口網站中，選取 [所有服務]  ，搜尋 Azure 資料庫移轉服務，然後選取 [Azure 資料庫移轉服務]  。

    ![找出 Azure 資料庫移轉服務的所有執行個體](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. 在 [Azure 資料庫移轉服務]  畫面上，搜尋您建立的執行個體名稱，然後選取該執行個體。

3. 選取 [+ 新增移轉專案]  。

4. 在上**新增移轉專案**畫面上，指定專案名稱，在**Source server type**文字方塊中，選取**SQL Server**中**目標伺服器型別**文字方塊中，選取**Azure SQL Database 受控執行個體**，然後針對**選擇活動型別**，選取**SSIS 封裝移轉**.

   ![建立 DMS 專案](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. 選取 [Create] \(建立\)  以建立專案。

## <a name="specify-source-details"></a>指定來源詳細資料

1. 在 [移轉來源詳細資料]  畫面上，指定來源 SQL Server 的連線詳細資料。

2. 如果您尚未在伺服器上安裝信任的憑證，請選取 [信任伺服器憑證]  核取方塊。

    未安裝信任的憑證時，SQL Server 會在執行個體啟動時產生自我簽署憑證。 此憑證用來加密用戶端連線的認證。

    > [!CAUTION]
    > 使用自我簽署憑證加密的 SSL 連線不會提供增強式安全性。 這種連線容易受到攔截式攻擊。 在生產環境中，或在連線到網際網路的伺服器上，您不應該仰賴使用自我簽署憑證的 SSL。

   ![來源詳細資料](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. 選取 [ **儲存**]。

## <a name="specify-target-details"></a>指定目標詳細資料

1. 在 **移轉目標詳細資料**畫面上，指定目標的連線詳細資料。

     ![目標詳細資料](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. 選取 [ **儲存**]。

## <a name="review-the-migration-summary"></a>檢閱移轉摘要

1. 在 [移轉摘要]  畫面上的 [活動名稱]  文字方塊中，指定移轉活動的名稱。

2. 針對**SSIS 專案和環境覆寫選項**，指定是否要覆寫或忽略現有的 SSIS 專案和環境。

    ![移轉專案摘要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 檢閱並確認與移轉專案相關聯的詳細資料。

## <a name="run-the-migration"></a>執行移轉

* 選取 [執行移轉]  。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
