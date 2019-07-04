---
title: 包含檔案
description: 包含檔案
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446587"
---
## <a name="generate-the-certificate-signing-request-file"></a>產生憑證簽署要求檔案

Apple Push Notification Service (APNs) 使用憑證來驗證您的通知服務。 遵循下列指示來建立必要的推播憑證，以便傳送和接收通知。 如需這些概念的詳細資訊，請參閱 [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) 官方文件。

產生憑證簽署要求 (CSR) 檔案，Apple 會用此檔案來產生簽署的推播憑證。

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 此工具可從啟動控制板上的 [公用程式]  資料夾或 [其他]  資料夾開啟。

1. 選取 [金鑰鏈存取]  ，並展開 [憑證助理]  ，然後選取 [從憑證授權單位要求憑證]  。

    ![使用金鑰鏈存取要求新憑證](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. 選取您的**使用者電子郵件地址**，輸入**一般名稱**值，並確定您已指定 [儲存至磁碟]  ，然後選取 [繼續]  。 請將 [CA 電子郵件地址]  留空，因為這不是必要資訊。

    ![必要的憑證資訊](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. 在 [另存新檔]  中輸入 CSR 檔案的名稱，在 [位置]  中選取位置，然後選取 [儲存]  。

    ![選擇憑證的檔案名稱](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    此動作會在選取的位置儲存 CSR 檔案。 預設位置是**桌面**。 請記住為檔案選擇的位置。

接著，向 Apple 註冊您的應用程式，並啟用推播通知，然後上傳這個匯出的 CSR 建立推播憑證。

## <a name="register-your-app-for-push-notifications"></a>針對推播通知註冊應用程式

若要將通知推送至 iOS 應用程式，請向 Apple 註冊您的應用程式，並註冊進行推播通知。  

1. 如果您尚未註冊應用程式，請瀏覽至 Apple Developer Center 上的 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)。 然後，以您的 Apple ID 登入，並依序選取 [識別碼]  、[應用程式識別碼]  、[+]  來註冊新的應用程式。

    ![iOS 佈建入口網站應用程式識別碼頁面](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. 將新應用程式的下列三個值予以更新，然後選取 [繼續]  ：

   * **名稱**：在 [應用程式識別碼描述]  區段的 [名稱]  方塊中輸入應用程式的描述性名稱。

   * **套件組合識別碼**：在 [明確的應用程式識別碼]  區段中，以[應用程式分發指南](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) (英文) 中所提的 `<Organization Identifier>.<Product Name>` 形式輸入 [套件組合識別碼]  。 [組織識別碼]  和 [產品名稱]  值必須符合當您建立 Xcode 專案時使用的組織識別碼和產品名稱。 在下列螢幕擷取畫面中，*NotificationHubs* 值會作為組織識別碼，*GetStarted* 值會作為產品名稱。 請確定**套件組合識別碼**值符合您 Xcode 專案中的值，好讓 Xcode 使用正確的發行設定檔。

   * **推播通知**：勾選 [應用程式服務]  區段中的 [推播通知]  選項。

     ![註冊新應用程式識別碼的表單](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     此動作會產生您的應用程式識別碼，並要求您確定此資訊。 選取 [註冊]  確認新的應用程式識別碼。

     選取 [註冊]  之後，您會看見 [註冊完成]  畫面，如下圖所示。 選取 [完成]  。

     ![應用程式識別碼註冊完成並顯示權利](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. 在 Developer Center 的 [應用程式識別碼]  底下，找出您建立的應用程式識別碼，並選取該資料列。

    ![應用程式識別碼清單](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    選取應用程式識別碼以顯示應用程式詳細資料，然後選取底部的 [編輯]  按鈕。

    ![編輯應用程式識別碼頁面](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. 捲動到畫面底部，然後選取 [開發推播 SSL 憑證]  區段下方的 [建立憑證]  按鈕。

    ![為應用程式識別碼建立憑證的按鈕](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    您現在會看到**新增 iOS 憑證**助理。

    > [!NOTE]
    > 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 只要確定在傳送通知時使用相同的憑證類型即可。

1. 選取 [選擇檔案]  ，瀏覽到您在第一個工作中儲存 CSR 檔案的位置，然後選取 [產生]  。

    ![產生的憑證 CSR 上傳頁面](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. 在入口網站建立憑證之後，請選取 [下載]  按鈕，然後選取 [完成]  。

    ![產生的憑證 CSR 下載頁面](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    這會下載憑證並將其儲存到您電腦中的 [下載]  資料夾。

    ![在 [下載] 資料夾中找出憑證檔案](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 依預設，下載的開發憑證會名為 **aps_development.cer**。

1. 選取下載的推播憑證 **aps_development.cer**。

    此動作會將新的憑證安裝在金鑰鏈中，如下圖所示：

    ![顯示新憑證的金鑰鏈存取憑證清單](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 雖然憑證中的名稱可能會不同，不過名稱會加上 **Apple Development iOS Push Services** 前置詞。

1. 在 Keychain Access 中，以滑鼠右鍵按一下您在 [憑證]  類別中建立的新推播憑證。 選取 [匯出]  、為檔案命名、選取 [.p12]  格式，然後選取 [儲存]  。

    ![以 p12 格式匯出憑證](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    記下匯出的 .p12 憑證的檔案名稱和位置。 其可用來啟用 APNs 驗證功能。

    > [!NOTE]
    > 本教學課程會建立名為 **QuickStart.p12** 的檔案。 Your file name and location might be different.

## <a name="create-a-provisioning-profile-for-the-app"></a>建立應用程式的佈建設定檔

1. 在 [iOS 佈建入口網站](https://go.microsoft.com/fwlink/p/?LinkId=272456)中，依序選取 [佈建設定檔]  和 [全部]  ，然後選取 [+]  來建立新的設定檔。 您會看到 [新增 iOS 佈建設定檔]  精靈。

    ![佈建設定檔清單](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 選取 [開發]  下的 [iOS 應用程式開發]  作為佈建設定檔類型，然後選取 [繼續]  。

1. 接著，從 [應用程式識別碼]  下拉式清單選取您建立的應用程式識別碼，然後選取 [繼續]  。

    ![選取應用程式識別碼](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. 在 [選取憑證]  視窗中，選取用於程式碼簽署的一般開發憑證，然後選取 [繼續]  。 此憑證不是您建立的推播憑證。

    ![選取憑證](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. 接下來，選取要用來測試的裝置，然後選取 [繼續]  。

    ![選取裝置](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. 最後，在 [設定檔名稱]  中為設定檔挑選名稱，然後選取 [產生]  。

    ![選擇佈建設定檔名稱](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 當新的佈建設定檔建立好時，將其選取以下載並安裝在 Xcode 開發電腦上。 然後選取 [完成]  。

    ![下載佈建設定檔](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>建立通知中樞

在本節中，您會建立通知中樞，並使用您先前建立的 .p12 推播憑證，設定以 APNs 進行驗證的機制。 如果您想要使用已經建立的通知中樞，可以跳至步驟 5。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>使用 APNs 資訊設定您的通知中樞

1. 在 [Notification Services]  下，選取 [Apple (APNS)]  。

1. 選取 [憑證]  。

1. 選取 [檔案] 圖示。

1. 選取您先前匯出的 .p12 檔案。

1. 指定正確的密碼。

1. 選取 [沙箱]  模式。 只有在您想傳送推播通知給從市集購買應用程式的使用者時，才可使用 [生產]  模式。

    ![在 Azure 入口網站中設定 APNS 憑證](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

您現在已使用 APNs 來設定通知中樞。 您也擁有可用來註冊應用程式和傳送推播通知的連接字串。
