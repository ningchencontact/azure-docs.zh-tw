---
title: 管理 Azure IoT Central 應用程式 | Microsoft Docs
description: 了解如何以系統管理員的身分管理 Azure IoT Central 應用程式
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ebca3060e902c0a45619c2bd64240537cd95308d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811846"
---
# <a name="administer-your-iot-central-application"></a>管理您的 IoT Central 應用程式

建立 IoT Central 應用程式之後，您可以移至 [管理] 區段：

- 管理應用程式設定
- 管理使用者
- 管理角色
- 檢視您的帳單
- 將試用版轉換為預付型方案
- 匯出資料
- 管理裝置連線
- 使用存取權杖

若要存取並使用 [管理] 區段，您必須屬於 Azure IoT Central 應用程式的**管理員**角色。 如果您建立 Azure IoT Central 應用程式，就會自動將您指派給該應用程式的**管理員**角色。 本文中的[管理使用者](#manage-users)一節會進一步說明將 [系統管理員] 角色指派給其他使用者的方法。

## <a name="manage-application-settings"></a>管理應用程式設定

### <a name="change-application-name-and-url"></a>變更應用程式名稱和 URL
在 [應用程式設定] 頁面上，您可以變更應用程式的名稱和 URL，然後選擇 [儲存]。

![[應用程式設定] 頁面](media/howto-administer-experimental/image0-a.png)

> [!Note]
> 如果您變更 URL，舊的 URL 可能會被其他 Azure IoT Central 客戶所取用。 如果發生這種情況，您將無法再使用該 URL。 當您變更 URL 時，舊的 URL 就不再運作，而您必須通知使用者應使用新的 URL。

### <a name="prepare-and-upload-image"></a>準備及上傳映像
若要變更應用程式影像，請參閱[準備及上傳影像至您的 Azure IoT Central 應用程式](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。

### <a name="copy-an-application"></a>複製應用程式
您可以建立任何應用程式複本，並減去其中的任何裝置執行個體、裝置資料歷程記錄和使用者資料。 這個複本將會是您必須支付費用的預付型方案應用程式。 如此一來，您無法建立試用版應用程式。

按一下 [複製] 按鈕。 在對話方塊中，輸入新預付型方案應用程式的詳細資料。 接著，按一下 [複製] 按鈕來確認您想要繼續進行。 在[建立應用程式](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)快速入門中，深入了解此表單中的欄位。

![[應用程式設定] 頁面](media/howto-administer-experimental/appCopy2.png)

當應用程式複製作業成功之後，您就可以移至透過使用所出現的連結複製應用程式所建立的新應用程式。

![[應用程式設定] 頁面](media/howto-administer-experimental/appCopy3.png)

> [!Note]
> 複製應用程式也會複製規則和動作的定義。 但是，由於不會將可以存取原始應用程式的使用者複製到所複製的應用程式，因此，您必須手動將使用者新增到動作，例如，向必要使用者寄送電子郵件。 一般而言，檢查規則和動作以確定它們在新應用程式中為最新狀態是個不錯的主意。

### <a name="delete-an-application"></a>刪除應用程式

> [!Note]
> 若要刪除應用程式，您必須在建立應用程式時所選擇的 Azure 訂用帳戶中具有刪除資源的權限。 若要深入了解，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

使用 [刪除] 按鈕可永久刪除您的 IoT Central 應用程式。 如此將會永久刪除所有與該應用程式相關聯的資料。

## <a name="manage-users"></a>管理使用者

### <a name="add-users"></a>新增使用者

每個使用者都必須具有使用者帳戶，才能登入並存取 Azure IoT Central 應用程式。 Azure IoT Central 支援 Microsoft 帳戶 (MSA) 和 Azure Active Directory (Azure AD) 帳戶。 Azure IoT Central 目前不支援 Azure Active Directory 群組。

如需詳細資訊，請參閱 [Microsoft 帳戶說明](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入門：將新的使用者新增到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

1. 若要將使用者新增至 IoT Central 應用程式，請前往 [管理] 區段中的 [使用者] 頁面。

    ![使用者清單](media/howto-administer-experimental/image1.png)

1. 若要新增使用者，在 [使用者] 頁面上，選擇 [+ 新增使用者]。

1. 從 [角色] 下拉式清單中選擇使用者的角色。 請在本文的[管理角色](#manage-roles)一節中深入了解角色。

    ![角色選取](media/howto-administer-experimental/image3.png)

    > [!NOTE]
    >  若要大量新增使用者，請針對所有想要新增的使用者，輸入以分號分隔的使用者識別碼。 從 [角色] 下拉式清單中選擇角色。 然後選取 [儲存]。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>編輯指派給使用者的角色

角色一經指派之後，就無法變更。 若要變更指派給使用者的角色，請刪除該使用者，然後以不同的角色再次新增該使用者。

### <a name="delete-users"></a>刪除使用者

若要刪除使用者，請選取 [使用者] 頁面上的一或多個核取方塊。 然後選取 [刪除]。

## <a name="manage-roles"></a>管理角色

角色可讓您控制組織中的哪些人員可以在 IoT Central 中執行各種不同的工作。 有三個角色您可以指派給應用程式的使用者。 

### <a name="administrator"></a>系統管理員

擔任 [系統管理員] 角色的使用者能夠存取應用程式中的所有功能。

系統會將建立應用程式的使用者自動指派給**管理員**角色。 必須至少有一個使用者擔任 [系統管理員] 角色。

### <a name="application-builder"></a>應用程式建置者

擔任 [應用程式建置者] 角色的使用者除了管理應用程式之外，可以執行應用程式中的所有工作。 也就是說，建置者可以建立、編輯和刪除裝置範本及裝置、管理裝置集合，以及執行分析和作業。 建置者無法存取應用程式的 [管理] 區段。

### <a name="application-operator"></a>應用程式操作員

擔任 [應用程式操作員] 角色的使用者無法對裝置範本進行變更，也無法管理應用程式。 也就是說，操作員可以新增和刪除裝置、管理裝置集合，以及執行分析和作業。 操作員無法存取應用程式的 [應用程式建置者] 和 [管理] 頁面。


## <a name="view-your-bill"></a>檢視您的帳單

若要檢視帳單，請移至 [管理] 區段中的 [帳單] 頁面。 Azure 帳單頁面即會在新的索引標籤中開啟，您可在其中看見每個 Azure IoT Central 應用程式的帳單。

### <a name="convert-your-trial-to-pay-as-you-go"></a>將試用版轉換為預付型方案

您可以將試用版應用程式轉換為預付型方案應用程式。 以下是這類應用程式之間的差異。

- **試用版**應用程式可以免費試用 7 天。 此類應用程式可在到期前隨時轉換成預付型方案。
- **預付型方案**應用程式會依據裝置收費，前 5 個裝置免費。

在 [Azure IoT Central 定價頁面](https://azure.microsoft.com/pricing/details/iot-central/)上深入了解定價。
    
若要完成此自助程序，請遵循這些步驟：

1. 請移至 [管理] 區段中的 [帳單] 頁面。 

    ![試用狀態](media/howto-administer-experimental/freetrialbilling.png)

1. 按一下 [轉換為預付型方案]。 

    ![轉換試用](media/howto-administer-experimental/convert.png)

1. 選取適當的 Azure Active Directory，然後選取要用於預付型方案應用程式的 Azure 訂用帳戶。

1. 按一下 [轉換] 之後，您的應用程式現在是預付型方案應用程式並開始計費。

## <a name="export-data"></a>匯出資料

您可以啟用 [連續資料匯出]，將計量、裝置和裝置範本資料匯出至 Azure Blob 儲存體帳戶。 深入了解[如何匯出資料](#howto-export-data)。

## <a name="manage-device-connection"></a>管理裝置連線

使用此處的金鑰和憑證，在應用程式中大規模連線裝置。 深入了解[連線裝置](#concepts-connectivity)。

## <a name="use-access-tokens"></a>使用存取權杖

產生存取權杖，在開發人員工具中使用它們。 目前沒有一個屬於 IoT Central Explorer 的開發人員工具可用來監視屬性和設定中的裝置訊息和變更。 深入了解 [IoT Central Explorer](#howto-use-iotc-explorer)。 

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>使用 Azure SDK 進行控制平面作業

IoT Central 的 Azure Resource Manager SDK 套件適用於 Node、Python、C#、Ruby、Java 與 Go。 這些程式庫支援適用於 IoT central 的控制平面作業，可讓您建立、列出、更新或刪除 IoT Central 應用程式。 它們也提供協助程式來處理每個語言專屬的驗證和錯誤處理。 

您可以在 [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) 上找到如何使用 Azure Resource Manager SDK 的範例。

若要深入了解，請在 GitHub 上查看這些套件。

| 語言 | 存放庫 | Package |
| ---------| ---------- | ------- |
| 節點 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>後續步驟

您現在已了解如何管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [設定裝置範本](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
