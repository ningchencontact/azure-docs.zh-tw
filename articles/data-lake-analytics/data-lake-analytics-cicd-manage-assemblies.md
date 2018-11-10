---
title: 針對 Azure Data Lake 管理 CI/CD 管線中之 U-SQL 組件的最佳做法
description: 了解搭配 Azure DevOps 管理 CI/CD 管線中之 U-SQL C# 組件的最佳做法。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/30/2018
ms.openlocfilehash: 0f6f581d90b025dd538eb9b79da1a0addd4259f7
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670285"
---
# <a name="best-practice-of-managing-u-sql-assemblies-in-cicd-pipeline"></a>管理 CI/CD 管線中之 U-SQL 組件的最佳做法

在本文中，您會了解如何搭配新推出的 U-SQL 資料庫專案來管理 U-SQL 組件原始程式碼。 您也可以了解如何使用 Azure DevOps 設定持續整合和部署 (CI/CD) 管線以進行組件註冊。

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>使用 U-SQL 資料庫專案來管理組件原始程式碼

[U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md)是 Visual Studio 中的專案類型，能協助開發人員快速而輕鬆地開發、管理及部署 U-SQL 資料庫。 所有的 U-SQL 資料庫專案 (除了認證以外) 皆可以搭配 U-SQL 資料庫專案進行管理。 

管理 C# 組件原始程式碼和組件註冊 DDL U-SQL 指令碼的建議方式為：

* 使用 **U-SQL 資料庫專案**來管理組件註冊 U-SQL 指令碼。
* 使用**類別庫 (適用於 U-SQL 應用程式)** 來管理適用於使用者定義運算子、函式及彙總工具 (UDO/UDF/UDAG) 的 C# 原始程式碼和相依性。
* 使用「U-SQL 資料庫專案」來參考「類別庫」專案。 

「U-SQL 資料庫專案」可以參考「類別庫 (適用於 U-SQL 應用程式)」專案。 在 U-SQL 資料庫中註冊的組件，可以使用來自此「類別庫 (適用於 U-SQL 應用程式)」專案的已參考 C# 原始程式碼來建立。

您可以遵循下列步驟以建立專案及新增參考：
1. 透過 [檔案] > [新增] > [專案] 來建立「類別庫 (適用於 U-SQL 應用程式)」專案。 該專案位於 [Azure Data Lake] > [U-SQL] 節點底下。
   ![Data Lake Tools for Visual Studio--建立 C# 類別庫專案](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
2. 將您的使用者定義 C# 程式碼新增到「類別庫 (適用於 U-SQL 應用程式)」專案中。 
3. 透過 [檔案] > [新增] > [專案] 來建立 U-SQL 專案。 該專案位於 [Azure Data Lake] > [U-SQL] 節點底下。
   ![Data Lake Tools for Visual Studio--建立 U-SQL 資料庫專案](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
4. 新增適用於該 U-SQL 資料庫專案的 C# 類別庫專案參考。

    ![Data Lake Tools for Visual Studio--新增 U-SQL 資料庫專案參考](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake Tools for Visual Studio--新增 U-SQL 資料庫專案參考](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)
5. 以滑鼠右鍵按一下該專案 > [加入新項目] 來在 U-SQL 資料庫專案中建立組件指令碼。
   ![Data Lake Tools for Visual Studio--新增組件指令碼](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)
6. 開啟組件指令碼，並在組件設計檢視中，從 [從參考建立組件] 下拉式功能表中選擇所參考的組件。

    ![Data Lake Tools for Visual Studio--從參考建立組件](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. 新增 [受控相依性] 和 [其他檔案] (若有的話)。 在新增其他檔案時，工具會使用相對路徑來確保能在本機電腦和稍後的組建電腦上找到組件。 

位於編輯器視窗底部的 **@_DeployTempDirectory** 是預先定義的變數，能將工具指向建置輸出資料夾。 在建置輸出資料夾中，每個組件都具有以組件名稱命名的子資料夾。 所有 Dll 和其他檔案都在該子資料夾中。 

## <a name="build-a-u-sql-database-project"></a>建置 U-SQL 資料庫專案

U-SQL 資料庫專案的建置輸出，是一個名稱加上 `.usqldbpack` 尾碼的 U-SQL 資料庫部署套件。 `.usqldbpack` 套件是一個 ZIP 檔案，其中的 **DDL** 資料夾含有單一 U-SQL 指令碼中的所有 DDL 陳述式，而 **Temp** 資料夾則含有組件的所有已建置 DLL 和額外檔案。

## <a name="deploy-a-u-sql-database"></a>部署 U-SQL 資料庫

您可以使用 Visual Studio 或部署 SDK，將 `.usqldbpack` 套件部署到本機帳戶或 Azure Data Lake Analytics 帳戶。 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>在 Visual Studio 中部署 U-SQL 資料庫

您可以在 Visual Studio 中透過 U-SQL 資料庫專案或 `.usqldbpack` 套件部署 U-SQL 資料庫。

#### <a name="deploy-through-a-u-sql-database-project"></a>透過 U-SQL 資料庫專案部署

1.  以滑鼠右鍵按一下 U-SQL 資料庫專案，然後選取 [部署]。
2.  在 [部署 U-SQL 資料庫精靈] 中，選擇要部署資料庫的目的地 [ADLA 帳戶]。 本機帳戶和 ADLA 帳戶兩者均可支援。
3.  [資料庫來源] 會自動填入，並指向專案建置輸出資料夾中的 .usqldbpack 套件。
4.  在 [資料庫名稱] 中輸入名稱，以建立資料庫。 如果目標 Azure Data Lake Analytics 帳戶已有相同名稱的資料庫，在資料庫專案中定義的所有物件均會建立，但不會重新建立資料庫。
5.  若要部署 U-SQL 資料庫，請按一下 [提交]。 所有資源 (組件和其他檔案) 隨即會上傳，而且包含所有 DDL 陳述式的 U-SQL 作業也會提交。

    ![Data Lake Tools for Visual Studio--部署 U-SQL 資料庫專案](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Data Lake Tools for Visual Studio--部署 U-SQL 資料庫專案精靈](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-u-sql-database-in-azure-devops"></a>在 Azure DevOps 中部署 U-SQL 資料庫

`PackageDeploymentTool.exe` 提供程式設計和命令列介面，協助您部署 U-SQL 資料庫。 SDK 包含在 [U-SQL SDK Nuget 套件](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位於 `build/runtime/PackageDeploymentTool.exe`。

在 Azure DevOps 中，您可以使用命令列工作及此 SDK 來設定自動化管線以進行 U-SQL 資料庫重新整理。 [深入了解 SDK，以及如何為 U-SQL 資料庫部署設定 CI/CD 管線](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)。

## <a name="next-steps"></a>後續步驟

* [如何設定 Azure Data Lake Analytics 的 CI/CD 管線](data-lake-analytics-cicd-overview.md)
* [如何測試 Azure Data Lake Analytics 程式碼](data-lake-analytics-cicd-test.md)
* [在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)
