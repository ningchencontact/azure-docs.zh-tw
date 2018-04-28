---
title: 開發 Azure-SSIS 整合執行階段的付費或授權元件 | Microsoft Docs
description: 本文說明 ISV 如何開發及安裝 Azure SSIS 整合執行階段的付費或授權自訂元件
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>開發 Azure-SSIS 整合執行階段的付費或授權自訂元件

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>問題 - Azure SSIS IR 需要不同的方法

Azure SSIS 整合執行階段的本質會帶來幾項挑戰，使得一般授權方法不足以用來在內部部署環境安裝自訂元件。

-   Azure-SSIS IR 的節點是動態的，隨時可以配置或釋放。 例如，您可以啟動或停止節點以管理成本，或者透過各種不同的節點大小相應增加和相應減少。 因此，藉由使用電腦專屬資訊 (例如 MAC 位址或 CPU 識別碼) 將第三方元件授權繫結至特定節點就不可行。

-   您也可以將 Azure SSIS IR 相應縮小或相應放大，讓節點數目可以隨時縮小或擴展。

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>解決方案 - 適用於授權繫結和驗證的 Windows 環境變數和 SSIS 系統變數

由於上一節中所述的傳統授權方法限制，Azure-SSIS IR 針對第三方元件的授權繫結和驗證，提供 Windows 環境變數和 SSIS 系統變數。 ISV 可以使用這些變數來取得 Azure-SSIS IR 的唯一且具持續性的資訊，例如叢集識別碼和叢集節點計數。 使用此資訊，ISV 就可以將其元件的授權繫結至 Azure-SSIS IR 作為叢集，而且當客戶啟動或停止、相應增加或相應減少、相應縮小或相應放大，或者以任何方式重新設定 Azure-SSIS IR 時也不會變更的識別碼。

下圖顯示典型安裝、啟用和授權繫結，以及使用這些新變數的第三方元件驗證流程：

![授權元件的安裝](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>範例的指示
1. ISV 可以在各種 SKU 或層級提供其授權元件 (例如，單一節點、最多 5 個節點、最多 10 個節點，依此類推)。 當客戶購買產品時，ISV 會提供對應的產品金鑰。 ISV 也會提供 Azure 儲存體 Blob 容器，其中包含 ISV 安裝指令碼和相關聯的檔案。 客戶可以將這些檔案複製到他們自己的儲存體容器，並且使用自己的產品金鑰進行修改 (例如，藉由執行 `IsvSetup.exe -pid xxxx-xxxx-xxxx`)。 然後客戶可以使用其容器的 SAS URI 作為參數，佈建或重新設定 Azure-SSIS IR。 如需詳細資訊，請參閱 [Azure-SSIS 整合執行階段自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)。

2. 當 Azure-SSIS IR 已佈建或重新設定時，ISV 安裝會在每個節點上執行以查詢 Windows 環境變數 (`SSIS_CLUSTERID` 和 `SSIS_CLUSTERNODECOUNT`)。 然後 Azure-SSIS IR 會將其授權產品的叢集識別碼和產品金鑰提交給 ISV 啟用伺服器，來產生啟用金鑰。

3. 接收啟用金鑰之後，ISV 安裝程序可以在每個節點上將金鑰儲存在本機 (例如，在「登錄」中)。

4. 當客戶執行套件 (該套件在 Azure-SSIS IR 的節點上使用 ISV 授權元件) 時，套件會讀取本機儲存的啟用金鑰，並且根據節點的叢集識別碼來加以驗證。 套件也可以選擇性地將叢集節點計數報告至 ISV 啟用伺服器。

    以下是驗證啟用金鑰以及報告叢集節點計數的程式碼範例：

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="next-steps"></a>後續步驟

-   [Azure-SSIS 整合執行階段自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS 整合執行階段的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)