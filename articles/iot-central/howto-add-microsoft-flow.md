---
title: 在 Microsoft Flow 中使用 Azure IoT Central 連接器建置工作流程 | Microsoft Docs
description: 在 Microsoft Flow 中使用 IoT Central 連接器，觸發工作流程及建立、 取得、 更新、 刪除裝置和工作流程中執行命令。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: c50271e28db104522c0e8811508fa882dc316eab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235813"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>在 Microsoft Flow 中使用 IoT Central 連接器建置工作流程

*本主題適用於建置員和系統管理員。*

使用 Microsoft Flow，將商務使用者所依賴的多個應用程式和服務的工作流程自動化。 在 Microsoft Flow 中使用 IoT Central 連接器時，您可以在規則於 IoT Central 中被觸發時觸發工作流程。 IoT 中心或任何其他應用程式所觸發的工作流程，在中，您可以使用動作中 IoT Central 連接器來：
- 建立裝置
- 取得裝置資訊
- 更新裝置的屬性和設定
- 在裝置上執行命令
- 刪除裝置

請了解可將 IoT Central 連線至其他服務 (例如行動裝置和 Microsoft Teams 等) 的[這些 Microsoft Flow 範本](https://aka.ms/iotcentralflowtemplates)。

## <a name="prerequisites"></a>必要條件

- 隨用隨付應用程式
- 可登入 Flow 的 Microsoft 的個人、公司或學校帳戶 ([深入了解 Microsoft Flow 計劃](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>觸發程序工作流程

本節將說明如何在 Flow 行動應用程式時 IoT Central 中的規則觸發程序中的行動裝置通知觸發程序。 您可以使用內嵌的 Microsoft Flow 設計工具的 IoT Central 應用程式內建置這整個工作流程。

1. 首先，[在 IoT Central 中建立規則](howto-create-telemetry-rules.md)。 儲存規則條件之後，請選取**Microsoft Flow 動作**做為新的動作。 對話方塊隨即開啟一個視窗讓您設定您的工作流程。 您已登入的 IoT Central 使用者帳戶將用來登入 Microsoft Flow 中。

    ![建立新的 Microsoft Flow 動作](media/howto-add-microsoft-flow/createflowaction.png)

1. 您會看到一份工作流程，您可以存取，而連接到 IoT Central 這項規則。 按一下 **探索範本**或是**新增 > 從範本建立**和您可以選擇任何可用的範本。 

    ![可用的 Microsoft Flow 範本](media/howto-add-microsoft-flow/flowtemplates1.png)

1. 系統會提示您登入您選擇的範本中的連接器。 連接器會登入之後，您將登陸在設計工具來建立您的工作流程。 工作流程具有已填入您的應用程式和規則的 IoT Central 觸發程序。

1. 您可以自訂工作流程自訂動作並加入新的動作所傳遞的資訊。 在此範例中，動作是 **通知-傳送行動通知**。 您可以納入 IoT Central 規則中的「動態內容」  ，將裝置名稱和時間戳記等重要資訊傳至您的通知。

    > [!NOTE]
    > 選取 **查看更多**動態內容視窗以取得觸發此規則的度量和屬性值中的文字。

    ![動態窗格開啟時的流程編輯動作](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. 當您完成編輯您的動作，請選取**儲存**。 您將被導向至工作流程的概觀頁面。 在此處您可以看到執行歷程記錄，並將其與其他同事共用。

    > [!NOTE]
    > 如果您想讓 IoT Central 應用程式中的其他使用者編輯此規則，您必須在 Microsoft Flow 中與他們共用規則。 請在工作流程中新增他們的 Microsoft Flow 帳戶，作為擁有者。

1. 如果您返回至您的 IoT Central 應用程式中，您會看到此規則會在 [動作] 區域中有 Microsoft Flow 的動作。

您也可以建置使用 IoT Central 連接器，直接從 Microsoft Flow 的工作流程。 然後，您可以選擇連接到哪個 IoT Central 應用程式。

## <a name="create-a-device-in-a-workflow"></a>在工作流程中建立裝置

本節說明如何使用 Microsoft Flow 行動應用程式，藉由按下行動裝置的按鈕在 IoT Central 中建立新的裝置。 您可以在流程中使用此動作，而在某個裝置新增於他處時建立新的裝置，將 Dynamics 之類的 ERP 系統與 IoT Central 整合。

1. 首先，在 Microsoft Flow 中建立空的工作流程。

1. 搜尋**適用於行動裝置的 [流程] 按鈕**，作為觸發程序。

1. 在此觸發程序中，新增名為**裝置名稱**的文字輸入。 請將描述文字變更為**輸入新裝置的裝置名稱**。

1. 新增動作。 搜尋 [Azure IoT Central - 建立裝置]  動作。

1. 選擇您的應用程式，並從下拉式清單中選擇用來建立裝置的裝置範本。 您會看到展開以顯示所有裝置屬性和設定的動作。

1. 選取 [裝置名稱] 欄位。 在動態內容窗格中，選擇 [裝置名稱]  。 這個值會從輸入的使用者透過行動裝置應用程式中，輸入，並為您 IoT Central 中的新裝置的名稱。 在此範例中，唯一的必要欄位是裝置名稱，會以紅色星號指出。 另一個裝置範本可能會有多個必須填入才能建立新裝置的必要欄位。

    ![流程建立裝置動作動態窗格](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (選擇性) 根據您建立新裝置時的需要，填寫其他欄位。

1. 最後，儲存您的工作流程。

1. 在 Microsoft Flow 行動應用程式中試用您的工作流程。 移至應用程式中的 [按鈕]  索引標籤。 您應該會看到 [按鈕] -> [建立新的裝置工作流程]。 請輸入新裝置的名稱，並確認它顯示於 IoT Central 中！

    ![流程建立裝置的行動裝置螢幕擷取畫面](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>更新工作流程中的裝置

本節說明如何使用 Microsoft Flow 行動應用程式，藉由按下行動裝置的按鈕來更新 IoT Central 中的裝置設定和屬性。

1. 首先，在 Microsoft Flow 中建立空的工作流程。

1. 搜尋**適用於行動裝置的 [流程] 按鈕**，作為觸發程序。

1. 在此觸發程序中，新增如 [位置]  文字輸入之類、且與您要變更的設定或屬性相對應的輸入。 請變更描述文字。

1. 新增動作。 搜尋 [Azure IoT Central - 更新裝置]  動作。

1. 從下拉式清單中選擇您的應用程式。 現在您將需要所要更新之現有裝置的識別碼。 

    > [!NOTE] 
    > **您必須使用在 URL 中找到 ID**您想要更新之裝置的裝置詳細資料頁面。 在 [裝置總管] 的裝置清單中找到的裝置識別碼不正確的權杖，才能在 Microsoft Flow。

    ![IoT 中心識別碼 URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. 您可以更新裝置名稱。 若要更新裝置的任何屬性和設定，您必須在 [裝置範本]  下拉式清單中針對您要更新的裝置選取裝置範本。 動作圖格會展開以顯示您可以更新的所有屬性和設定。

    ![流程更新裝置工作流程](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. 選取您要更新的每個屬性和設定。 在動態內容窗格中，從觸發程序中選擇對應的輸入。 在此範例中，[位置] 值會向下傳播，以更新裝置的 [位置] 屬性。

1. 最後，儲存您的工作流程。

1. 在 Microsoft Flow 行動應用程式中試用您的工作流程。 移至應用程式中的 [按鈕]  索引標籤。 您應該會看到 [按鈕] -> [更新裝置工作流程]。 輸入您的輸入內容，並查看裝置在 IoT Central 中更新的情形！

## <a name="get-device-information-in-a-workflow"></a>取得工作流程中的裝置資訊

您可以取得其識別碼使用的裝置資訊**Azure IoT Central-取得裝置**動作。 
> [!NOTE] 
> **您必須使用在 URL 中找到 ID**您想要更新之裝置的裝置詳細資料頁面。 在 [裝置總管] 的裝置清單中找到的裝置識別碼不正確的權杖，才能在 Microsoft Flow。

您可以取得資訊，例如裝置名稱、 裝置範本名稱、 屬性值，以及要傳遞至工作流程中的後續動作的設定值。 以下是範例工作流程，一起客戶名稱屬性值從裝置傳送至 Microsoft Teams。

   ![流程取得裝置的工作流程](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>在工作流程中的裝置上執行命令
您可以使用其識別碼指定在裝置上執行命令**Azure IoT Central-執行命令**動作。 

> [!NOTE] 
> **您必須使用在 URL 中找到 ID**您想要更新之裝置的裝置詳細資料頁面。 在 [裝置總管] 的裝置清單中找到的裝置識別碼不正確的權杖，才能在 Microsoft Flow。
    
您可以挑選要執行，並傳入命令的參數，透過此動作的命令。 以下是範例工作流程執行裝置重新開機命令，從 Microsoft Flow 行動裝置應用程式中的按鈕。

   ![流程取得裝置的工作流程](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>刪除工作流程中的裝置

您可以刪除其識別碼使用的裝置**Azure IoT Central-刪除裝置**動作。 
> [!NOTE] 
> **您必須使用在 URL 中找到 ID**您想要更新之裝置的裝置詳細資料頁面。 在 [裝置總管] 的裝置清單中找到的裝置識別碼不正確的權杖，才能在 Microsoft Flow。

以下是在 Microsoft Flow 行動應用程式中按下按鈕以刪除裝置的範例工作流程。

   ![流程刪除裝置工作流程](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>疑難排解

如果您無法建立對 Azure IoT Central 連接器的連線，以下提供一些秘訣供您參考。

1. 目前並不支援 Microsoft 個人帳戶 (例如 @hotmail.com、@live.com、@outlook.com 網域)。 您必須使用 Azure Active Directory (AD) 的工作或學校帳戶。

2. 若要在 Microsoft Flow 中使用 IoT Central 連接器，您必須至少已登入 IoT Central 應用程式一次。 否則，該應用程式不會出現在 [應用程式] 下拉式清單中。

3. 如果您使用 Azure AD 帳戶時收到錯誤，請開啟 Windows PowerShell 和系統管理員身分執行下列 commandlet。

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>後續步驟

既然您已了解如何使用 Microsoft Flow 建立工作流程，建議的下一個步驟是[管理裝置](howto-manage-devices.md)。

