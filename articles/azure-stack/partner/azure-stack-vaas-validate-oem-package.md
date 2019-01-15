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
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: e3b0de577186cb7eb032a2042d234a0ffa2e3bb9
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105537"
---
# <a name="validate-oem-packages"></a>驗證 OEM 套件

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

您可以在已完成的解決方案驗證有韌體或驅動程式方面的變更時，測試新的 OEM 套件。 當您的套件通過測試後，Microsoft 會加以簽署。 您的測試必須包含更新的 OEM 延伸模組套件，且驅動程式和韌體均須通過 Windows Server 標誌和 PCS 測試。

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> 上傳或提交套件之前，請先檢閱[建立 OEM 套件](azure-stack-vaas-create-oem-package.md)以了解預期的封裝格式和內容。

## <a name="managing-packages-for-validation"></a>管理要驗證的套件

使用**套件驗證**工作流程來驗證套件時，您必須提供 **Azure 儲存體 Blob** 的 URL。 此 Blob 是在部署時安裝於解決方案上的 OEM 套件。 請使用您在設定期間建立的 Azure 儲存體帳戶來建立 Blob (請參閱[設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md))。

### <a name="prerequisite-provision-a-storage-container"></a>必要條件：佈建儲存體容器

在儲存體帳戶中建立套件 Blob 的容器。 此容器可用於您所有的套件驗證回合。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至在[設定您的驗證即服務資源](azure-stack-vaas-set-up-resources.md)中建立的儲存體帳戶。
2. 在左側刀鋒視窗的 [Blob 服務] 下方，選取 [容器]。
3. 選取功能表列中的 [+ 容器]，並提供容器的名稱，例如 `vaaspackages`。

### <a name="upload-package-to-storage-account"></a>將套件上傳至儲存體帳戶

1. 準備您要驗證的套件。 如果您的套件有多個檔案，請將其壓縮為 `.zip` 檔案。
2. 在 [Azure 入口網站](https://portal.azure.com)中選取套件容器，然後選取功能表列中的 [上傳] 以上傳套件。
3. 選取要上傳的套件 `.zip` 檔案。 請將 [Blob 類型] (即 [區塊 Blob]) 和 [區塊大小] 保留為預設值。

> [!NOTE]
> 請確實將 `.zip` 內容放在 `.zip` 檔案的根目錄上。 套件中不應有任何子資料夾。

### <a name="generate-package-blob-url-for-vaas"></a>產生用於 VaaS 的套件 Blob URL

在 VaaS 入口網站中建立**套件驗證**工作流程時，您必須為包含套件的 Azure 儲存體 Blob 提供 URL。

#### <a name="option-1-generating-an-account-sas-url"></a>選項 1：產生帳戶 SAS URL

1. 在 [Azure 入口網站](https://portal.azure.com/)中移至儲存體帳戶，然後瀏覽至包含您套件的 .zip

2. 從捷徑功能表內選取 [產生 SAS]

3. 從 [權限] 選取 [讀取]

4. 將 [開始時間] 設為目前時間，並將 [結束時間] 至少設為**開始時間**的 48 小時後。 如果您會使用相同的套件執行其他測試，請考慮增加**結束時間**以符合您的測試長度。 在**結束時間**後，任何透過 VaaS 排程的測試將會失敗，且必須產生新的 SAS。

5. 選取 [產生 Blob SAS 權杖和 URL]

在 VaaS 入口網站中啟動新的**套件驗證**工作流程時，請使用 **Blob SAS URL**。

#### <a name="option-2-using-public-read-container"></a>選項 2：使用公用讀取容器

> [!CAUTION]
> 此選項會開啟您的容器以供匿名唯讀存取。

1. 依照[授與容器和 Blob 的匿名使用者權限](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)一節中的指示操作，為套件容器授與 [僅對 Blob 有公開讀取權限]。

2. 在套件容器中，選取容器中的套件 Blob 以開啟 [屬性] 窗格。

3. 複製 **URL**。 在 VaaS 入口網站中啟動新的**套件驗證**工作流程時，請使用此值。

## <a name="apply-monthly-update"></a>套用每月更新

[!INCLUDE [azure-stack-vaas-workflow-section_update-azs](includes/azure-stack-vaas-workflow-section_update-azs.md)]

## <a name="create-a-package-validation-workflow"></a>建立套件驗證工作流程

1. 登入 [VaaS 入口網站](https://azurestackvalidation.com)。

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. 選取 [套件驗證] 圖格上的 [啟動]。

    ![套件驗證工作流程圖格](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. 輸入在部署時安裝於解決方案上的 OEM 套件所具備的 Azure 儲存體 Blob URL。 如需指示，請參閱[產生用於 VaaS 的套件 Blob URL](#generate-package-blob-url-for-vaas)。

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > 建立工作流程後即無法修改環境參數。

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    系統會將您重新導向至測試摘要頁面。

## <a name="run-package-validation-tests"></a>執行套件驗證測試

1. 在 [套件驗證測試摘要] 頁面中，您會看到完成驗證所需的測試清單。 此工作流程中的測試大約會執行 24 小時。

    在驗證工作流程中，**排程**測試時會使用您在建立工作流程期間所指定的工作流程層級一般參數 (請參閱 [Azure Stack 驗證即服務的工作流程一般參數](azure-stack-vaas-parameters.md))。 如果有任何測試參數值無效，您就必須依照[修改工作流程參數](azure-stack-vaas-monitor-test.md#change-workflow-parameters)中的指示重新提供參數。

    > [!NOTE]
    > 對現有的執行個體排程驗證測試時，將建立新的執行個體而取代入口網站中的舊執行個體。 舊執行個體的記錄仍會保留，但無法從入口網站存取。  
    測試順利完成後，**排程**動作就會停用。

2. 選取將執行測試的代理程式。 如需新增本機測試執行代理程式的相關資訊，請參閱[部署本機代理程式](azure-stack-vaas-local-agent.md)。

3. 對下列各項測試，步驟四和步驟五：
    - OEM 延伸模組套件驗證
    - 雲端模擬引擎

4. 從內容功能表中選取 [排程]，以開啟排程測試執行個體的提示。

5. 檢閱測試參數，然後選取 [提交] 以排程要執行的測試。

所有測試均順利完成後，請將您 VaaS 解決方案的名稱和套件驗證傳送至 [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com)，以要求套件簽署。

## <a name="next-steps"></a>後續步驟

- [監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)