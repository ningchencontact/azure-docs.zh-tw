---
title: 在 Azure Data Factory 中，將自我裝載整合執行時間設定為 SSIS 的 proxy |Microsoft Docs
description: 瞭解如何設定自我裝載 Integration Runtime 做為 Azure SSIS Integration Runtime 的 proxy。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: de9e0a936c68f181665e44ea6115f60c6dc60e98
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179062"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>設定自我裝載 IR 作為 ADF 中的 Azure SSIS IR 的 proxy
本文說明如何使用設定為 proxy 的自我裝載 IR，在 Azure Data Factory （ADF）中的 Azure SSIS Integration Runtime （IR）上執行 SQL Server Integration Services （SSIS）套件。  這項功能可讓您存取內部部署資料，而不需要將[您的 AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。  當您的公司網路具有過於複雜的設定/限制原則，讓您在其中插入您的 Azure SSIS IR 時，這會很有用。

這項功能會將包含具有內部部署資料來源之資料流程工作的套件分割成兩個預備工作：在自我裝載 IR 上執行的第一個作業會先將資料從內部部署資料來源移到您 Azure Blob 儲存體的臨時區域中，而在您的 Azure SSIS IR 上執行的第二個，會將資料從暫存區域移至預期的資料目的地。

這項功能也提供其他優點/功能，因為它可讓您在 Azure SSIS IR 尚未支援的區域中布建自我裝載 IR，並允許您在資料來源的防火牆上自我裝載 IR 的公用靜態 IP 位址等等。

## <a name="prepare-self-hosted-ir"></a>準備自我裝載 IR
若要使用這項功能，您必須先依照如何布建[AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文中的說明來建立 ADF，並在其下布建您的 AZURE ssis ir （如果尚未這麼做）。

接著，您必須依照[如何建立自我裝載 ir](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)一文，在布建您的 AZURE SSIS ir 的同一個 ADF 下布建自我裝載 ir。

最後，您必須在內部部署機器/Azure 虛擬機器（VM）上下載並安裝最新版的自我裝載 IR，以及額外的驅動程式和執行時間，如下所示：
- 請從[這裡](https://www.microsoft.com/download/details.aspx?id=39717)下載並安裝最新版的自我裝載 IR。
- 如果您在套件中使用 OLEDB 連接器，請在已安裝自我裝載 IR 的同一部電腦上下載並安裝相關的 OLEDB 驅動程式（如果您尚未這麼做）。  如果您使用舊版 OLEDB driver for SQL Server （SQLNCLI），您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=50402)下載64位版本。  如果您使用最新版本的 OLEDB driver for SQL Server （內含 MSOLEDBSQL.H），您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=56730)下載64位版本。  如果您針對其他資料庫系統（例如于 postgresql、MySQL、Oracle 等）使用 OLEDB 驅動程式，您可以從其各自的網站下載64位版本。
- 請在已安裝自我C++裝載 IR 的同一部電腦上下載並安裝 Visual （VC）執行時間（如果您尚未這麼做）。  您可以從[這裡](https://www.microsoft.com/download/details.aspx?id=40784)下載64位版本。

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>準備 Azure Blob 儲存體連結服務以進行預備
請依照[如何建立 ADF 連結服務一](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)文中的說明，在布建您的 AZURE SSIS IR 的相同 ADF 下建立 Azure Blob 儲存體連結服務。  請確定下列事項：
- 已選取**資料存放區**的**Azure Blob 儲存體**
- 已選取**AutoResolveIntegrationRuntime**以透過**整合執行時間連接**
- 為**驗證方法**選取了**帳戶金鑰**/ **SAS URI** /**服務主體**

![準備 Azure Blob 儲存體連結服務以進行預備](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>使用自我裝載 IR 作為 proxy 來設定 Azure SSIS IR
準備好您的自我裝載 IR 和 Azure Blob 儲存體連結服務以進行預備，您現在可以使用自我裝載 IR 來設定新的/現有的 Azure SSIS IR，以作為 ADF 入口網站/應用程式上的 proxy。  如果您現有的 Azure SSIS IR 正在執行，請先將它停止，然後再重新開機它。

在 [**高級設定**] 頁面上，核取 [**設定自我裝載的 Integration Runtime 做為您的 Azure SSIS Integration Runtime 的 proxy** ] 核取方塊、選取您的自我裝載 IR 和用於暫存的 Azure Blob 儲存體連結服務，並指定 Blob**暫存路徑**的容器名稱（如果您想要的話）。

![使用自我裝載 IR 作為 proxy 來設定 Azure SSIS IR](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>啟用 SSIS 套件以透過 proxy 連接
使用最新的 SSDT 與 SSIS 專案的 Visual Studio 擴充功能，可從[這裡](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下載，或作為可從[這裡](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)下載的獨立安裝程式，您可以找到已在 OLEDB/中新增的新**ConnectByProxy**屬性一般檔案連接管理員。  

當您使用 OLEDB/一般檔案來源來設計包含資料流程工作的新封裝，以存取內部部署資料庫/檔案時，您可以在相關連線管理員的 [屬性] 面板上，將此屬性設定為**True** ，藉以啟用此屬性。

![啟用 ConnectByProxy 屬性](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

您也可以在執行現有的封裝時啟用此屬性，而不需要逐一手動變更它們。  有2個選項：
- 使用要在您的 Azure SSIS IR 上執行的最新 SSDT，開啟、重建和重新部署包含這些套件的專案：接著，您可以將屬性設為 True，以便在從 SSMS 執行封裝時，出現在 [執行封裝] 快顯視窗的 [**連線管理員**] 索引標籤上的相關連線管理員中，設定為 [ **True** ]。

  ![啟用 ConnectByProxy property2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  您也可以將屬性設定為 True，以針對在 ADF 管線中執行封裝時，[[執行 SSIS 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)] 的 [**連線管理員**] 索引標籤上顯示的相關連線管理員，將它設為**True** 。
  
  ![啟用 ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- 重新部署包含要在 SSIS IR 上執行之封裝的專案：接著，可以藉由提供屬性路徑`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`來啟用屬性，並在從 SSMS 執行封裝時，將它設定為**True** ，做為 [執行封裝] 快顯視窗的 [ **Advanced** ] 索引標籤上的屬性覆寫。

  ![啟用 ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  您也可以藉`\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`由提供屬性路徑來啟用屬性，並在執行 ADF 管線中的封裝時，將它設定為**True** ，以做為 [[執行 SSIS 封裝活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)] 的 [**屬性覆**寫] 索引標籤上的屬性覆寫。
  
  ![啟用 ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Debug 第一個和第二個預備工作
在自我裝載 IR 上，您可以在`C:\ProgramData\SSISTelemetry`資料夾中找到執行時間記錄，並在資料夾中`C:\ProgramData\SSISTelemetry\ExecutionLog`尋找第一個暫存工作的執行記錄。  第二個預備工作的執行記錄可以在您的 SSISDB 或指定的記錄路徑中找到，這取決於您是在 SSISDB 或檔案系統/檔案共用/Azure 檔案儲存體中分別儲存封裝。  第一個預備工作的唯一識別碼也可以在第二個預備工作的執行記錄中找到，例如 

![第一個預備工作的唯一識別碼](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>第一和第二個預備工作的計費
在自我裝載 IR 上執行的第一項預備工作，將會以與在自我裝載 IR 上執行的任何資料移動活動相同的方式計費，如同[ADF 資料管線定價](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)一文中所指定。

在您的 Azure SSIS IR 上執行的第二個預備工作將不會分開計費，但執行中的 Azure SSIS IR 會依照[AZURE SSIS ir 定價](https://azure.microsoft.com/pricing/details/data-factory/ssis/)一文中的指定計費。

## <a name="current-limitations"></a>目前的限制
- 目前僅支援 OLEDB/一般檔案連接管理員和 OLEDB/一般檔案來源。
- 目前僅支援以**帳戶金鑰**/ **SAS URI** /**服務主體**驗證設定 Azure Blob 儲存體連結服務。
- 目前僅支援在您布建 Azure SSIS IR 的相同 ADF 下布建的自我裝載 IR。
- 不支援 SSIS 變數和參數。

## <a name="next-steps"></a>後續步驟
將自我裝載 IR 設定為 Azure SSIS IR 的 proxy 之後，您就可以部署並執行封裝，以在 ADF 管線中以「執行 SSIS 套件」活動的形式存取內部部署資料，請參閱[在 adf 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).