---
title: 在 Azure Stack 驗證即服務中驗證原始設備製造商 (OEM) 套件 | Microsoft Docs
description: 了解如何使用「驗證即服務」來驗證原始設備製造商 (OEM) 套件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aae3ec8ff713959c5cc2485951aba025a6f89a1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113277"
---
# <a name="validate-oem-packages"></a>驗證 OEM 套件

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

您可以在已完成的解決方案驗證有韌體或驅動程式方面的變更時，測試新的 OEM 套件。 當您的套件通過測試後，Microsoft 會加以簽署。 您的測試必須包含更新的 OEM 延伸模組套件，且驅動程式和韌體均須通過 Windows Server 標誌和 PCS 測試。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 上傳或提交套件之前，請先檢閱[建立 OEM 套件](azure-stack-vaas-create-oem-package.md)以了解預期的封裝格式和內容。

## <a name="managing-packages-for-validation"></a>管理要驗證的套件

使用**套件驗證**工作流程來驗證套件時，您必須提供 **Azure 儲存體 Blob** 的 URL。 此 Blob 是測試簽署的 OEM 套件，該套件會在更新過程中安裝。 請使用您在設定期間建立的 Azure 儲存體帳戶來建立 Blob (請參閱[設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md))。

### <a name="prerequisite-provision-a-storage-container"></a>必要條件：佈建儲存體容器

在儲存體帳戶中建立套件 Blob 的容器。 此容器可用於您所有的套件驗證回合。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至在[設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md)中所建立的儲存體帳戶。

2. 在左側刀鋒視窗上的 [Blob 服務] 下方，選取 [容器]。

3. 從功能表列選取 [+ 容器]。
    1. 提供容器的名稱，例如 `vaaspackages`。
    1. 針對未經驗證的用戶端 (例如 VaaS) 選取所需的存取層級。 如需有關如何在每個案例中為套件授與 VaaS 存取權的詳細資訊，請參閱[處理容器存取層級](#handling-container-access-level)。

### <a name="upload-package-to-storage-account"></a>將套件上傳至儲存體帳戶

1. 準備您要驗證的套件。 這是 `.zip` 檔案，其內容必須與[建立 OEM 套件](azure-stack-vaas-create-oem-package.md)中所描述的結構相符。

    > [!NOTE]
    > 請確實將 `.zip` 內容放在 `.zip` 檔案的根目錄上。 套件中不應有任何子資料夾。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取套件容器，然後從功能表列選取 [上傳] 來上傳該套件。

1. 選取要上傳的套件 `.zip` 檔案。 保留下列設定的預設值：[Blob 類型] (即 [區塊 Blob]) 和 [區塊大小]。

### <a name="generate-package-blob-url-for-vaas"></a>產生用於 VaaS 的套件 Blob URL

在 VaaS 入口網站中建立**套件驗證**工作流程時，您必須為包含套件的 Azure 儲存體 Blob 提供 URL。 有些「互動式」的測試，包括 [每月 AzureStack 更新驗證] 和 [OEM 擴充功能套件驗證]，也會需要 URL 以封裝 Blob。

#### <a name="handling-container-access-level"></a>處理容器存取層級

VaaS 所需的最低存取層級取決於您是建立套件驗證工作流程，或是對「互動式」測試進行排程。

在 [私人] 和 [Blob] 存取層級的情況下，您必須透過為 VaaS 提供[共用存取簽章](https://docs.microsoft.com/en-us/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS)，以暫時授與存取權給套件 Blob。 [容器] 存取層級並不會要求您產生 SAS URL，但會允許在未經授權情況下存取容器和其 Blob。

|存取層級 | 工作流程需求 | 測試需求 |
|---|---------|---------|
|私人 | 為每個套件 Blob 產生 SAS URL ([選項 1](#option-1-generate-a-blob-sas-url))。 | 產生帳戶層級的 SAS URL 並手動新增套件 Blob 名稱 ([選項 2](#option-2-construct-a-container-sas-url))。 |
|Blob | 提供 Blob URL 屬性 ([選項 3](#option-3-grant-public-read-access))。 | 產生帳戶層級的 SAS URL 並手動新增套件 Blob 名稱 ([選項 2](#option-2-construct-a-container-sas-url))。 |
|容器 | 提供 Blob URL 屬性 ([選項 3](#option-3-grant-public-read-access))。 | 提供 Blob URL 屬性 ([選項 3](#option-3-grant-public-read-access))。

將存取權授與套件的選項會依據存取層級高低，由低至高排序。

#### <a name="option-1-generate-a-blob-sas-url"></a>選項 1：產生 Blob SAS URL

如果您儲存體容器的存取層級已設定為 [私人]，請使用此選項；其中容器不會針對容器或其 Blob 啟用公用讀取權限。

> [!NOTE]
> 這個方法不適用於「互動式」測試。 請參閱[選項 2：建構容器 SAS URL](#option-2-construct-a-container-sas-url)。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至您的儲存體帳戶，然後瀏覽至包含套件的 .zip。

2. 從操作功能表選取 [產生 SAS]。

3. 從 [權限] 選取 [讀取]。

4. 將 [開始時間] 設為目前時間，並將 [結束時間] 至少設為**開始時間**的 48 小時後。 如果您會使用相同的套件建立其他工作流程，請考慮針對您的測試時間長度延後 [結束時間]。

5. 選取 [產生 Blob SAS 權杖和 URL]。

將套件 Blob URL 提供給入口網站時，請使用 [Blob SAS URL]。

#### <a name="option-2-construct-a-container-sas-url"></a>選項 2：建構容器 SAS URL

如果您儲存體容器的存取層級設定為 [私人]，請使用此選項，且您需要提供套件 Blob URL 給「互動式」測試。 此 URL 也可以在工作流程層級使用。

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. 從 [允許的服務] 選項中選取 [Blob]。 請將其餘所有的選項取消選取。

1. 從 [允許的資源類型] 中選取 [容器] 和 [物件]。

1. 從 [允許的權限] 中選取 [讀取] 和 [列出]。 請將所有其餘選項取消選取。

1. 選取 [開始時間] 作為目前時間，並將 [結束時間] 設為 [開始時間] 至少 14 天後。 如果您會使用相同的套件執行其他測試，請考慮增加**結束時間**以符合您的測試長度。 在**結束時間**後，任何透過 VaaS 排程的測試將會失敗，且必須產生新的 SAS。

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    格式應如下所示：`https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. 修改產生的 SAS URL，以納入套件容器 `{containername}` 和套件 Blob 的名稱 `{mypackage.zip}`，如下所示：`https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    將套件 Blob URL 提供給入口網站時，請使用此值。

#### <a name="option-3-grant-public-read-access"></a>選項 3：授與公用讀取權限

如果可接受允許未經驗證的用戶端存取個別的 Blob 或容器 (進行「互動式」測試時)，請使用此選項。

> [!CAUTION]
> 此選項會開啟您的 Blob，以供匿名唯讀存取。

1. 依照[授與容器和 Blob 的匿名使用者權限](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)一節中的指示操作，將套件容器的存取層級設定為 [Blob] 或 [容器]。

    > [!NOTE]
    > 如果您將套件 URL 提供給「互動式」測試，必須將**完整公用讀取權限**授與容器才能繼續測試。

1. 在套件容器中，選取套件 Blob 以開啟 [屬性] 窗格。

1. 複製 **URL**。 將套件 Blob URL 提供給入口網站時，請使用此值。

## <a name="create-a-package-validation-workflow"></a>建立套件驗證工作流程

1. 登入 [VaaS 入口網站](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 選取 [套件驗證] 圖格上的 [啟動]。

    ![套件驗證工作流程圖格](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 對需要 Microsoft 簽章的測試簽署 OEM 套件，輸入 Azure 儲存體 Blob URL。 如需指示，請參閱[產生用於 VaaS 的套件 Blob URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 建立工作流程後即無法修改環境參數。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    系統會將您重新導向至測試摘要頁面。

## <a name="required-tests"></a>必要的測試

以下是 OEM 套件驗證所需的測試：

- OEM 擴充功能套件驗證
- 雲端模擬引擎

## <a name="run-package-validation-tests"></a>執行套件驗證測試

1. 在 [套件驗證測試摘要] 頁面中，您會執行適合您案例的部分所列測試。

    在驗證工作流程中，**排程**測試時會使用您在建立工作流程期間所指定的工作流程層級一般參數 (請參閱 [Azure Stack 驗證即服務的工作流程一般參數](azure-stack-vaas-parameters.md))。 如果有任何測試參數值無效，您就必須依照[修改工作流程參數](azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示重新提供參數。

    > [!NOTE]
    > 對現有的執行個體排程驗證測試時，將建立新的執行個體而取代入口網站中的舊執行個體。 舊執行個體的記錄仍會保留，但無法從入口網站存取。  
    > 測試順利完成後，**排程**動作就會停用。

2. 選取將執行測試的代理程式。 如需新增本機測試執行代理程式的相關資訊，請參閱[部署本機代理程式](azure-stack-vaas-local-agent.md)。

3. 若要完成 OEM 擴充功能套件驗證，請從操作功能表選取 [排程]，以開啟對測試執行個體進行排程的提示。

4. 檢閱測試參數，然後選取 [提交] 來排程 OEM 擴充功能套件驗證以供執行。

    OEM 擴充功能套件驗證分為兩個手動步驟：Azure Stack 更新與 OEM 更新。

   1. **選取** UI 中的 [執行] 以執行前置檢查指令碼。 這是自動化的測試，需要大約 5 分鐘來完成，且您不需要執行任何動作。

   1. 完成前置檢查指令碼之後，請執行手動步驟：使用 Azure Stack 入口網站來**安裝**最新的可用 Azure Stack 更新。

   1. 在戳記上**執行** Test-AzureStack。 如果發生任何失敗，請停止測試並連絡 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)。

       如需如何執行 Test-AzureStack 命令的相關資訊，請參閱[驗證 Azure Stack 系統狀態](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)。

   1. **選取** [下一步] 以執行前置檢查指令碼。 這是自動化測試，且為 Azure Stack 更新程序的最後一步。

   1. **選取** [執行] 以執行 OEM 更新的前置檢查指令碼。

   1. 前置檢查完成之後，請執行手動步驟：透過入口網站**安裝** OEM 擴充套件。

   1. 在戳記上**執行** Test-AzureStack。

      > [!NOTE]
      > 同樣地，如果發生任何失敗，請停止測試並連絡 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)。 這個步驟至關重要，因為它可以防止發生需要重新部署的情況。

   1. **選取** [下一步] 以執行前置檢查指令碼。 這是 OEM 更新步驟的最後一步。

   1. 請在測試結束時回答任何剩餘的問題，並**選取** [提交]。

   1. 這是互動式測試的最後一步。

5. 檢閱 OEM 擴充功能套件驗證的結果。 測試成功後，排程雲端模擬引擎以供執行。

若要提交套件簽署要求，請將和此執行有關的方案名稱與套件驗證名稱傳送至 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)
