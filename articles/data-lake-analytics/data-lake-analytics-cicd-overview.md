---
title: 如何設定 Azure Data Lake Analytics 的 CI/CD 管線 | Microsoft Docs
description: 了解如何為 Azure Data Lake Analytics 設定持續整合和持續部署。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a55c8fd95409de4fb1ea725d234de907f79d3c7b
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890723"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>如何設定 Azure Data Lake Analytics 的 CI/CD 管線

透過本文件，可了解如何設定 U-SQL 作業和 U-SQL 資料庫的 CI/CD 管線。

## <a name="cicd-for-u-sql-job"></a>U-SQL 作業的 CI/CD

Azure Data Lake Tools for Visual Studio 提供有 U-SQL 專案類型，有助於編排 U-SQL 指令碼。 使用 U-SQL 專案來管理 U-SQL 程式碼，有利於實現進階 CI/CD 案例。

## <a name="build-u-sql-project"></a>建置 U-SQL 專案

可以藉由 MSBuild 傳遞對應的參數，以此建置 U-SQL 專案。 請遵循下列步驟，設定 U-SQL 專案的建置流程。

### <a name="project-migration"></a>專案移轉

設定 U-SQL 專案的建置工作之前，請務必使用最新版本的 U-SQL 專案。 在編輯器中開啟 U-SQL 專案檔案，並檢查您是否具有下列匯入項目：

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

如果沒有，則有兩種專案移轉選項：

- 選項 1：將舊有的匯入項目變更為前述項目。
- 選項 2：在 Azure Data Lake Tools for Visual Studio 2.3.3000.0 之後版本中開啟舊專案。 舊有的專案範本將會自動升級為最新版本。 2.3.3000.0 版之後的新建專案會直接使用新範本。

### <a name="get-nuget-package"></a>取得 NuGet 套件

MSBuild 並未內建對 U-SQL 專案類型的支援。 若要新增這項功能，需要為解決方案新增至 [Microsoft.Azure.DataLake.USQL.SDK Nuget package](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) 的參考，藉此新增所需的語言服務。

若要新增 NuGet 套件參考，請在方案總管中以滑鼠右鍵按一下解決方案，並選擇 [管理 NuGet 套件]。 您也可以在解決方案資料夾內新增名為 `packages.config` 的檔案，再將以下內容新增到檔案中。

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>管理 U-SQL 資料庫參考

如果 U-SQL 專案中的 U-SQL 指令碼有 U-SQL 資料庫物件的查詢陳述式 (如查詢 U-SQL 資料表或參考組件)，在建置該 U-SQL 專案之前，需要參考內有上述物件定義的對應 U-SQL 資料庫專案。

[深入了解 U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md)

### <a name="build-u-sql-project-with-msbuild-command-line"></a>使用 MSBuild 命令列建置 U-SQL 專案

在移轉專案及取得 NuGet 套件之後，可以使用以下額外引數呼叫標準 MSBuild 命令列，建置您的 U-SQL 專案：

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder
``` 

引數的定義和值為：

* USQLSDKPath=<U-SQL Nuget package>\build\runtime：此參數代表 U-SQL 語言服務之 NuGet 套件的安裝路徑。
* USQLTargetType=Merge 或 SyntaxCheck：
    * Merge：合併模式會編譯程式碼後置檔案 (如 .cs、.py 及 .r 檔案)，將所產生的使用者定義程式碼程式庫 (如 dll 二進位、Python 或 R 程式碼) 內嵌在 U-SQL 指令碼中。
    * SyntaxCheck：SyntaxCheck 模式會先將程式碼後置檔案合併到 U-SQL 指令碼中，再編譯 U-SQL 指令碼來驗證您的程式碼。
* DataRoot=<DataRoot path>：唯有 SyntaxCheck 模式需要 DataRoot。 在使用 SyntaxCheck 模式建置指令碼時，MSBuild 會檢查指令碼中對資料庫物件的參考。 建置之前，請務必在組建電腦的 DataRoot 資料夾內設定相符的本機環境，加入來自 U-SQL 資料庫的參考物件。 若要管理這些資料庫相依性，您也可以[參考 U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。 請注意，MSBuild 只會檢查資料庫物件參考，不會檢查檔案。

### <a name="continuous-integration-with-visual-studio-team-service"></a>與 Visual Studio Team Services 持續整合

除了命令列之外，客戶還可以使用 Visual Studio Build 或 MSBuild 工作，在 Visual Studio Team Service 中建置 U-SQL 專案。 若要設定建置工作，請務必：

1.  新增 NuGet 還原工作以取得解決方案參考的 NuGet 套件 (包括 `Azure.DataLake.USQL.SDK`)，讓 MSBuild 找得到 U-SQL 語言目標。 

    ![U-SQL 專案的 Data Lake Set CI CD MSBuild 工作](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  設定 MSBuild 引數。您可以在 Visual Studio Build 或 MSBuild 工作中設定引數 (如下圖所示)，也可以在 VSTS 組建定義中定義這些引數的變數。

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory)
    ```

    ![U-SQL 專案的 Data Lake Set CI CD MSBuild 變數](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>U-SQL 專案建置輸出

執行建置後，將會編譯 U-SQL 專案中的所有指令碼，並輸出到名為 `USQLProjectName.usqlpack` 的 ZIP 檔案。 壓縮的建置輸出將保留專案中的資料夾結構。

>[!NOTE]
>
>每個 U-SQL 指令碼的程式碼後置檔案會以內嵌陳述式的形式合併為指令碼建置的輸出。
>

## <a name="test-u-sql-script"></a>測試 U-SQL 指令碼

Azure Data Lake 提供適用於 U-SQL 指令碼和 C# UDO/UDAG/UDF 的測試專案：
* [了解如何新增 U-SQL 指令碼的測試案例及擴充 C# 指令碼](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [了解如何在 Visual Studio Team Service 中執行這些測試案例](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>U-SQL 作業部署

在透過組建和測試程序驗證程式碼之後，你可以透過 **Azure PowerShell 工作**直接從 Visual Studio Team Service 提交 U-SQL 作業。 也可以將指令碼部署到 Azure Data Lake Store/Azure Blob 儲存體，再[透過 Azure Data Factory 執行排程作業](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)。

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>透過 Visual Studio Team Service 提交 U-SQL 作業

U-SQL 專案的建置輸出是一個名為 **USQLProjectName.usqlpack** 的 ZIP 檔案，其中包括專案中的所有 U-SQL 指令碼。 您可以[在 Visual Studio Team Service 中使用 Azure PowserShell 工作](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)，並搭配以下範例 PowserShell 指令碼，直接從 Visual Studio Team Service 組建或發行管線提交 U-SQL 作業。

```powershell
param(
    [Parameter(Mandatory=$true)][string]$AnalyticsAccountName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, #Root folder (e.g. artifacts root folder)
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
        # [System.IO.Compression.ZipFile]::ExtractToDirectory($USQLPackfile, $UnzipOutput, 0)
        # $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
        # Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue | Where-Object {$_.DirectoryName -match $subFolder}

    return $USQLFiles
}

Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."
    # submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "($usqlFiles.IndexOf())Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        LogJobInformation $jobToSubmit
        
        Write-Output "waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $AnalyticsAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
        
        # ProcessResult $jobResult
    }
}

Function ProcessResult($jobResult)
{
    if ($jobResult.Result -eq "Failed")
    {
        Write-Error "Job Failed. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name), Log: $($jobResult.LogFolder)"
    }
    else
    {
        Write-Output "Job Succeeded. Job Id: $($jobResult.JobId), Job Name: $($jobResult.Name)"
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{

    Write-Output "Starting USQL script deployment..."

    # Submit ADLA jobs with usql scripts in given sub-folder.
    # Order is important here. Scripts with least dependency goes first followed 
    # by scripts which more dependencies.
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>透過 Azure Data Factory 部署 U-SQL 作業

除了直接從 Visual Studio Team Service 提交 U-SQL 作業之外，您還可以將建置的指令碼上傳到 Azure Data Lake Store/Azure Blob 儲存體，再[透過 Azure Data Factory 執行排程作業](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)。

在 [Visual Studio Team Service 中使用 Azure PowerShell 工作](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)，並搭配以下範例 PowerShell 指令碼，將 U-SQL 指令碼上傳到 Azure Data Lake Store 帳戶。

```powershell
param(
    [Parameter(Mandatory=$true)][string]$ADLSName, #ADLA account name
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot #Root folder (e.g. artifacts root folder)
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading DLL files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles
    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $usqlScripts.FullName -Destination "/ScriptResource2/$usqlScripts" -Force -Recurse
    # $files = @(get-childitem $usqlScripts -recurse)
    # foreach($file in $files)
    # {
    #    Write-Host "Uploading file: $($file.Name)"
    #    Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/ScriptResource/$file" -Force -Recurse
    # }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return $UnzipOutput
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>U-SQL 資料庫的 CI/CD

Azure Data Lake Tools for Visual Studio 提供 U-SQL 資料庫專案範本，協助開發人員輕鬆快速地開發、管理及部署 U-SQL 資料庫。 [深入了解 U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md)。

## <a name="build-u-sql-database-project"></a>建置 U-SQL 資料庫專案

### <a name="get-nuget-package"></a>取得 NuGet 套件

MSBuild 未提供內建的 U-SQL 資料庫專案類型支援。 若要新增這項功能，需要為解決方案新增至 [Microsoft.Azure.DataLake.USQL.SDK Nuget package](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) 的參考，藉此新增所需的語言服務。

若要新增 NuGet 套件參考，請在方案總管中以滑鼠右鍵按一下解決方案，並選擇 [管理解決方案的 NuGet 套件]，然後搜尋及安裝 NuGet 套件。 您也可以在解決方案資料夾內新增名為 "packages.config" 的檔案，再將以下內容新增到檔案中。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>使用 MSBuild 命令列建置 U-SQL 資料庫專案

若要建置 U-SQL 資料庫專案，您可以呼叫標準 MSBuild 命令列，再以額外引數的形式傳遞 U-SQL SDK NuGet 套件參考，如下圖所示：

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

引數 `USQLSDKPath=<U-SQL Nuget package>\build\runtime` 代表 U-SQL 語言服務之 NuGet 套件的安裝路徑。

### <a name="continuous-integration-with-visual-studio-team-service"></a>與 Visual Studio Team Services 持續整合

除了命令列之外，客戶還可以使用 **Visual Studio Build** 或 **MSBuild 工作**，在 Visual Studio Team Service 中建置 U-SQL 資料庫專案。 若要設定建置工作，請務必：

1.  新增 NuGet 還原工作以取得解決方案參考的 NuGet 套件 (包括 `Azure.DataLake.USQL.SDK`)，讓 MSBuild 找得到 U-SQL 語言目標。 

    ![U-SQL 資料庫專案的 Data Lake Set CI CD MSBuild 工作](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

2.  設定 MSBuild 引數。您可以在 Visual Studio Build 或 MSBuild 工作中設定引數 (如下圖所示)，也可以在 VSTS 組建定義中定義這些引數的變數。

```
/p:USQLSDKPath=$(Build.SourcesDirectory)/<your project name>/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.1019-preview/build/runtime
```

![U-SQL 資料庫專案的 Data Lake Set CI CD MSBuild 變數](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>U-SQL 資料庫專案建置輸出

U-SQL 資料庫專案的建置輸出，是一個名稱加上 `.usqldbpack` 尾碼的 U-SQL 資料庫部署套件。 `.usqldbpack` 套件是一個 ZIP 檔案，其中的 DDL 資料夾含有單一 U-SQL 指令碼中的所有 DDL 陳述式，而 Temp 資料夾則含有組件的所有 .dll 和額外檔案。

## <a name="test-table-valued-function-and-stored-procedure"></a>測試資料表值函式和預存程序

目前並不支援直接新增資料表值函式和預存程序的測試案例。 為了因應這種情況，您可以建立 U-SQL 專案，加入呼叫這些函式的 U-SQL 指令碼，然後再為它們撰寫測試案例。 請遵循以下步驟，針對在 U-SQL 資料庫專案中定義的資料表值函式和預存程序設定測試案例：

1.  建立測試用的 U-SQL 專案，再撰寫呼叫資料表值函式和預存程序的 U-SQL 指令碼。
2.  將資料庫參考新增到該 U-SQL 專案。 為了取得資料表值函式和預存程序定義，您需要參考含有 DDL 陳述式的資料庫專案。 [深入了解資料庫參考](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project) (英文)。
3.  新增用以呼叫資料表值函式和預存程序之 U-SQL 指令碼的測試案例。 [了解如何新增 U-SQL 指令碼的測試案例](data-lake-analytics-cicd-test.md#test-u-sql-scripts) (英文)。

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>透過 Visual Studio Team Service 部署 U-SQL 資料庫

`PackageDeploymentTool.exe` 提供程式設計和命令列介面，協助您部署 U-SQL 資料庫部署套件 (.usqldbpack)。 SDK 隨附於 [U-SQL SDK NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位於 build/runtime/PackageDeploymentTool.exe。 只要使用 `PackageDeploymentTool.exe`，您就可以將 U-SQL 資料庫部署到 Azure Data Lake Analytics 和本機帳戶。

>[!NOTE]
>
>U-SQL 資料庫部署的 PowerShell 命令列支援和 Visual Studio Team Service 發行工作支援正在開發中。
>

請遵循以下步驟，在 Visual Studio Team Service 中設定資料庫部署工作：

1. 在組建或發行管線中新增 PowerShell 指令碼工作，並執行以下 PowerShell 指令碼。 這項工作有助於取得 `PackageDeploymentTool.exe` 的 Azure SDK 相依性。 您可以設定 -outputfolder 參數，將這些相依性載入某個特定的資料夾。 將這個資料夾路徑傳遞給步驟 2 的 `PackageDeploymentTool.exe`。 

    ```powershell
    param (
        [string]$outputfolder = "RequiredDll",
        [string]$workingfolder = ""
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force

    echo "Copy required DLLs to output folder..."

    mkdir $outputfolder -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $outputfolder
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $outputfolder
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $outputfolder
    ```

2. 在組建或發行管線中新增**命令列工作**，然後填入呼叫 `PackageDeploymentTool.exe` 的指令碼。 範例指令碼如下所示︰ 

    * 本機部署 U-SQL 資料庫

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * 使用互動式驗證模式將 U-SQL 資料庫部署到 Azure Data Lake Analytics 帳戶：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * 使用秘密驗證將 U-SQL 資料庫部署到 Azure Data Lake Analytics 帳戶：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * 使用 certFile 驗證將 U-SQL 資料庫部署到 Azure Data Lake Analytics 帳戶：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**PackageDeploymentTool.exe h0 參數說明：**

**一般參數：**

|參數|說明|預設值|必要|
|---------|-----------|-------------|--------|
|Package|待部署之 U-SQL 資料庫部署套件的路徑|null|true|
|資料庫|待部署或建立之資料庫的名稱|master|false|
|LogFile|記錄檔的路徑，預設為標準輸出 (主控台)|null|false|
|LogLevel|記錄層級：詳細資訊、一般、警告、錯誤|LogLevel.Normal|false|

**本機部署參數：**

|參數|說明|預設值|必要|
|---------|-----------|-------------|--------|
|DataRoot|本機資料根資料夾的路徑|null|true|

**Azure Data Lake Analytics 部署參數：**

|參數|說明|預設值|必要|
|---------|-----------|-------------|--------|
|帳戶|依照帳戶名稱指定部署的目的地 Azure Data Lake Analytics 帳戶|null|true|
|ResourceGroup|Azure Data Lake Analytics 帳戶的 Azure 資源群組名稱|null|true|
|SubscriptionId|Azure Data Lake Analytics 帳戶的 Azure 訂用帳戶識別碼|null|true|
|租用戶|租用戶名稱 (AAD 目錄網域名稱，位於 Azure 入口網站的訂用帳戶管理頁面)|null|true|
|AzureSDKPath|在 Azure SDK 中搜尋相依組件的路徑|null|true|
|互動式|是否要使用互動模式來進行驗證|false|false|
|ClientId|非互動式驗證的 AAD 應用程式識別碼 (非互動式驗證的必要項目)|null|非互動式驗證的必要項目|
|Secrete|用於非互動式驗證的秘密/密碼，只應在信任/安全環境中使用|null|非互動式驗證的必要項目，也可以使用 SecreteFile|
|SecreteFile|儲存非互動式驗證之秘密/密碼的檔案；請務必妥善保管，並確認目前的使用者只能讀取|null|非互動式驗證的必要項目，也可以使用 Secrete|
|CertFile|儲存非互動式驗證之 X.509 憑證的檔案，預設值為使用用戶端秘密驗證|null|false|
|JobPrefix|資料庫部署 U-SQL DDL 作業的首碼|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>後續步驟

- [如何測試 Azure Data Lake Analytics 程式碼](data-lake-analytics-cicd-test.md)
- [在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL 資料庫專案開發 U-SQL 資料庫](data-lake-analytics-data-lake-tools-develop-usql-database.md)