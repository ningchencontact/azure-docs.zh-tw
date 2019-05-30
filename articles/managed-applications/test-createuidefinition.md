---
title: 測試 Azure 受控應用程式的 UI 定義 | Microsoft Docs
description: 說明如何測試透過入口網站建立 Azure 受控應用程式的使用者體驗。
author: tfitzmac
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 99ca319910be2cb20214172826eb40361abe72f0
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257640"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Azure 受控應用程式中測試您的入口網站介面

在後[建立 createUiDefinition.json 檔案](create-uidefinition-overview.md)受控的應用程式，您需要測試的使用者體驗。 若要簡化測試，使用沙箱環境，以載入您在入口網站中的檔案。 您不需要實際部署受控應用程式。 沙箱會提供您的使用者介面，在目前、 全螢幕的入口網站體驗。 或者，您可以使用 PowerShell 指令碼，測試介面，但它會使用舊版的入口網站檢視。 本文中會展示這兩種方法。 沙箱是建議用來預覽的介面。

## <a name="prerequisites"></a>必要條件

* **createUiDefinition.json** 檔案。 如果您沒有此檔案，請將複製[範例檔案](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)。

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="use-sandbox"></a>使用沙箱

1. 開啟[建立 UI 定義沙箱](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)。

   ![顯示沙箱](./media/test-createuidefinition/show-sandbox.png)

1. 取代 createUiDefinition.json 檔案的內容中的空白定義。 選取 **預覽**。

   ![選取 [預覽]](./media/test-createuidefinition/select-preview.png)

1. 您所建立的表單隨即顯示。 您可以逐步執行使用者體驗，並填入值。

   ![顯示表單](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>疑難排解

如果您的表單不會顯示在選取後**預覽**，您可能有語法錯誤。 尋找紅色指示器，右側的捲軸，並瀏覽至它。

![顯示語法錯誤](./media/test-createuidefinition/show-syntax-error.png)

如果未顯示您的表單，而您會看到雲端使用終止下拉式功能表的圖示，您的表單具有發生錯誤，例如遺漏的屬性。 在您的瀏覽器中開啟 Web Developer Tools。 [主控台]  會顯示關於介面的重要訊息。

![顯示錯誤](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>使用測試指令碼

若要在入口網站中測試您的介面，請將下列其中一個指令碼複製到本機電腦：

* [PowerShell 側載指令碼](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI 側載指令碼](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

若要在入口網站中查看您的介面檔案，請執行您已下載的指令碼。 此指令碼會在您的 Azure 訂用帳戶中建立儲存體帳戶，並將 createUiDefinition.json 檔案上傳至儲存體帳戶。 第一次執行指令碼時或儲存體帳戶已刪除後，將會建立儲存體帳戶。 如果 Azure 訂用帳戶中已有儲存體帳戶，則指令碼會重複使用該帳戶。 指令碼會開啟入口網站，並從儲存體帳戶載入您的檔案。

請提供儲存體帳戶的位置，並指定含有 createUiDefinition.json 檔案的資料夾。

對於 PowerShell，請使用：

```powershell
.\SideLoad-CreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

對於 Azure CLI，請使用：

```azurecli
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

如果 createUiDefinition.json 檔案位於和指令碼相同的資料夾內，而且您已建立儲存體帳戶，則不需要提供這些參數。

對於 PowerShell，請使用：

```powershell
.\SideLoad-CreateUIDefinition.ps1
```

對於 Azure CLI，請使用：

```azurecli
./sideload-createuidef.sh
```

指令碼會在瀏覽器中開啟新的索引標籤。 它會顯示入口網站，其中包含您用來建立受控應用程式的介面。

![檢視入口網站](./media/test-createuidefinition/view-portal.png)

提供欄位的值。 作業完成後，您會看到傳至範本的值。

![顯示值](./media/test-createuidefinition/show-json.png)

您可以使用這些值作為參數檔案來測試部署範本。

如果入口網站停止回應在 [摘要] 畫面中，在 [輸出] 區段中可能有 bug。 例如，您可能參考了不存在的控制項。 如果在輸出中的參數是空的參數可能會參考不存在的屬性。 例如，控制項的參考有效，但屬性參考無效。

## <a name="test-your-solution-files"></a>測試您的解決方案檔案

現在您已確認入口網站介面正常運作，接下來可以驗證您的 createUiDefinition 檔案是否與 mainTemplate.json 檔案正確整合。 您可以執行驗證指令碼測試來測試解決方案檔案的內容，包括 createUiDefinition 檔案。 指令碼會驗證 JSON 語法、檢查文字欄位上的 regex 運算式，並確定入口網站介面的輸出值符合您的範本參數。 如需執行此指令碼的相關資訊，請參閱[執行範本的靜態驗證檢查](https://github.com/Azure/azure-quickstart-templates/tree/master/test/template-validation-tests)。

## <a name="next-steps"></a>後續步驟

驗證入口網站介面後，請了解如何建立[在 Marketplace 中提供的 Azure 受控應用程式](publish-marketplace-app.md)。
