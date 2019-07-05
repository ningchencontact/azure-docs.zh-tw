---
title: Azure 資料方塊的邊緣裝置的存取、 電源和連線模式 |Microsoft Docs
description: 說明如何管理存取、 電源及可協助將資料傳輸至 Azure 的 Azure 資料方塊的邊緣裝置的連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 2426ecd9c480b0cc236295384ae04c60051f37ce
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442961"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>管理您的 Azure 資料方塊邊緣的存取、 電源和連線模式

本文說明如何管理您的 Azure 資料方塊邊緣的存取、 電源和連線能力的模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。

在本文中，您將了解：

> [!div class="checklist"]
> * 管理裝置存取
> * 管理連線模式
> * 管理電源


## <a name="manage-device-access"></a>管理裝置存取

存取您的資料方塊的邊緣裝置會受到使用的裝置密碼。 您可以透過變更密碼的本機 web UI。 您也可以重設裝置密碼，在 Azure 入口網站中。

### <a name="change-device-password"></a>變更裝置密碼

遵循下列步驟，在本機的使用者介面，來變更裝置密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]  。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 選取 [變更密碼]  。
 
### <a name="reset-device-password"></a>重設裝置密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]  。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 選取 **重設**。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>管理資源存取

若要建立您的資料方塊邊緣/資料方塊閘道，IoT 中樞和 Azure 儲存體資源，您會需要為參與者或更高的權限在資源群組層級。 您也需要對應的資源提供者註冊。 包含啟動金鑰和認證的任何作業，如 Azure Active Directory Graph API 的權限也是必要的。 這些都是以下列各節所述。

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>管理 Microsoft Azure Active Directory 圖形 API 權限

產生時資料方塊的邊緣裝置，或執行需要認證的任何作業的啟動金鑰，您會需要 Azure Active Directory Graph API 權限。 可能需要認證的作業：

-  與相關聯的儲存體帳戶中建立共用。
-  建立使用者可以存取裝置上的共用的人員。

您應該`User`您需要能夠存取 Active Directory 租用戶上`Read all directory objects`。 您不能來賓使用者，因為它們沒有權限`Read all directory objects`。 如果您是來賓，則作業，例如產生的啟用金鑰、 建立您的資料方塊的邊緣裝置、 建立使用者、 設定 Edge 上的共用計算角色、 重設裝置密碼會將會失敗。

如需有關如何為 Azure Active Directory Graph API 的使用者提供存取的詳細資訊，請參閱 <<c0> [ 系統管理員、 使用者和來賓使用者的預設存取](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)。

### <a name="register-resource-providers"></a>註冊資源提供者

若要佈建 Azure 資源 （在 Azure Resource Manager 模型中），您需要支援建立該資源的資源提供者。 例如，如果您佈建新的虛擬機器，您應該有 'Microsoft.Compute' 資源提供者可用的訂用帳戶中。
 
資源提供者註冊訂用帳戶層級。 根據預設，任何新的 Azure 訂用帳戶是一份常用的資源提供者預先註冊。 這份清單中不包含 'Microsoft.DataBoxEdge' 的資源提供者。

您不需要訂用帳戶層級的 「 存取 」 權限，讓使用者能夠建立資源，例如 'Microsoft.DataBoxEdge' 它們對擁有者權限，其資源群組內，只要這些資源的資源提供者已授與註冊。

您嘗試建立任何資源，請確定之前的資源提供者註冊訂用帳戶中。 如果未註冊資源提供者，您必須確定建立新的資源的使用者具有足夠的權限，註冊所需的資源提供者的訂用帳戶層級上。 如果您還沒有這麼做也，您會看到下列錯誤：

*訂用帳戶<Subscription name>註冊資源提供者的權限：Microsoft.DataBoxEdge.*


若要取得目前的訂用帳戶中的已註冊的資源提供者清單，請執行下列命令：

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

資料方塊的邊緣裝置`Microsoft.DataBoxEdge`應該註冊。 若要註冊`Microsoft.DataBoxEdge`，訂用帳戶系統管理員應該執行下列命令：

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

如需有關如何註冊資源提供者的詳細資訊，請參閱 <<c0> [ 解決的資源提供者註冊錯誤](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)。

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設完全連線模式，也可以部分連線，或完全中斷連接模式中執行您的裝置。 每種模式的說明如下：

- **完全連接**-這是裝置會運作正常的預設模式。 在雲端上傳和下載的資料會在此模式中啟用。 您可以使用 Azure 入口網站或本機 web UI 來管理裝置。

- **部分中斷**– 在此模式中，裝置無法上傳或下載資料但可透過 Azure 入口網站的任何共用。

    此模式通常使用於計量付費的衛星網路，其目標在於減少網路頻寬使用量。 裝置監視作業仍可能需要最低網路使用量。

- **已中斷連線** – 在此模式中，裝置完全與雲端中斷連線，且雲端上傳和下載皆已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]  。
2. 從下拉式清單中，選取您想要在裝置運作的模式。 您可以從選取**完全相連**，**部分連線**，並**完全中斷**。 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]  。

    ![連線模式](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理電源

您可以關閉或重新啟動您使用本機 web UI 中的實體裝置。 我們建議您重新啟動之前，讓共用離線，資料的伺服器，然後在裝置上。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]  。
2. 選取 **關機**或是**重新啟動**取決於您想要進行。

    ![電源設定](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 當提示確認，請選取**是**繼續進行。

> [!NOTE]
> 如果您關閉實體裝置時，您必須按下電源按鈕將它開啟裝置上。

## <a name="next-steps"></a>後續步驟

- 了解如何[管理共用](data-box-edge-manage-shares.md)。
