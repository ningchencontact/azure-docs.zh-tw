---
title: Azure 儲存體總管疑難排解指南 | Microsoft Docs
description: Azure 儲存體總管的偵錯工具技術總覽
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 69631b39403dedab56ed75cb145d464c0e1f747c
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935348"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 儲存體總管疑難排解指南

Microsoft Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 應用程式可以連線至裝載於 Azure、National Clouds 和 Azure Stack 上的儲存體帳戶。

本指南摘要說明儲存體總管中常見的問題解決方案。

## <a name="role-based-access-control-permission-issues"></a>以角色為基礎的存取控制許可權問題

[角色型存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)藉由將一組許可權組合成_角色_，來提供更細緻的 Azure 資源存取管理。 以下是您可以遵循的一些建議，讓 RBAC 在儲存體總管中運作。

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>我需要在儲存體總管中看到我的資源嗎？

如果您在使用 RBAC 存取儲存體資源時發生問題，可能是因為您尚未指派適當的角色。 下列各節說明目前存取儲存體資源所需的許可權儲存體總管。

如果您不確定您是否擁有適當的角色或許可權，請洽詢您的 Azure 帳戶管理員。

#### <a name="read-listget-storage-accounts"></a>讀取：列出/取得儲存體帳戶

您必須擁有列出儲存體帳戶的許可權。 您可以藉由指派「讀者」角色來取得此許可權。

#### <a name="list-storage-account-keys"></a>清單儲存體帳戶金鑰

儲存體總管也可以使用帳戶金鑰來驗證要求。 您可以使用功能更強大的角色（例如「參與者」角色）來取得金鑰的存取權。

> [!NOTE]
> 存取金鑰會授與不受限制的許可權給持有他們的任何人。 因此，通常不建議您將它們交給帳戶使用者。 如果您需要撤銷存取金鑰，您可以從[Azure 入口網站](https://portal.azure.com/)重新產生它們。

#### <a name="data-roles"></a>資料角色

您必須至少指派一個角色，以授與資源的存取權讀取資料。 例如，如果您需要列出或下載 blob，則至少需要「儲存體 Blob 資料讀取者」角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>為什麼我需要管理層角色才能在儲存體總管中看到我的資源？

Azure 儲存體有兩個層級的存取權：_管理_和_資料_。 訂閱和儲存體帳戶可透過管理層存取。 容器、blob 和其他資料資源都是透過資料層來存取。 例如，如果您想要從 Azure 取得儲存體帳戶的清單，請將要求傳送至管理端點。 如果您想要帳戶中的 blob 容器清單，請將要求傳送至適當的服務端點。

RBAC 角色可能包含管理或資料層存取的許可權。 例如，「讀者」角色會授與您唯讀存取管理層資源。

嚴格來說，「讀者」角色不提供資料層許可權，也不需要存取資料層。

儲存體總管可讓您收集必要資訊以連線到您的 Azure 資源，以輕鬆存取您的資源。 例如，若要顯示您的 blob 容器，儲存體總管會將清單容器要求傳送至 blob 服務端點。 若要取得該端點，儲存體總管會搜尋您有權存取的訂用帳戶和儲存體帳戶清單。 但是，若要尋找您的訂用帳戶和儲存體帳戶，儲存體總管也需要管理層的存取權。

如果您沒有授與任何管理層許可權的角色，儲存體總管就無法取得連接到資料層所需的資訊。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我無法取得系統管理員所需的管理層許可權，該怎麼辦？

目前尚無 RBAC 相關的解決方案。 因應措施是，您可以要求 SAS URI 以[附加至您的資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri)。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error:憑證鏈結中的自我簽署憑證 (和類似錯誤)

憑證錯誤是由下列兩種情況的其中一個所造成：

1. 應用程式是透過「透明 proxy」連線，這表示伺服器 (例如您公司的伺服器) 是使用自我簽署憑證攔截 HTTPS 流量、解密再加密。
2. 您正在執行應用程式，將自我簽署的 SSL 憑證插入您收到的 HTTPS 訊息。 會插入憑證的應用程式例子包括防毒軟體和網路流量檢查軟體。

當儲存體總管看見自我簽署或不受信任的憑證，就無法再得知收到的 HTTPS 訊息是否已被更改。 如果您有一份自我簽署憑證，便可以執行下列步驟，指示儲存體總管信任該憑證：

1. 取得憑證的 Base-64 編碼 X.509 (.cer) 複本
2. 按一下 [**編輯**] → [ **SSL 憑證**] → [匯**入憑證**]，然後使用檔案選擇器來尋找、選取及開啟 .cer 檔案

此問題也可能是因為有多個憑證 (根憑證與中繼憑證)。 必須新增這兩個憑證，才能解決錯誤。

如果您不確定憑證的來源，您可以嘗試下列步驟來尋找它：

1. 安裝 Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版足可應對)
    * Mac 和 Linux：應該包含在作業系統中
2. 執行 Open SSL
    * Windows：開啟安裝目錄，按一下 **/bin/** ，再按兩下 **openssl.exe**。
    * Mac 和 Linux：從終端機執行 **openssl**。
3. 執行 `s_client -showcerts -connect microsoft.com:443`
4. 尋找自我簽署憑證。 如果您不確定哪些是自我簽署的憑證，請尋找主體`("s:")`和發行者`("i:")`相同的任何位置。
5. 發現任何自我簽署的憑證時，請將每個憑證從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。
6. 開啟儲存體總管，按一下 [**編輯**] → [ **SSL 憑證**] → [匯**入憑證**]，然後使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果您找不到任何使用上述步驟的自我簽署憑證，請透過意見反應工具與我們聯繫以取得更多協助。 您也可以選擇從命令列使用`--ignore-certificate-errors`旗標啟動儲存體總管。 使用這個旗標啟動時，儲存體總管會忽略憑證錯誤。

## <a name="sign-in-issues"></a>登入問題

### <a name="blank-sign-in-dialog"></a>空白登入對話方塊

空白登入對話方塊最常見的原因是 ADFS 會要求儲存體總管執行重新導向，而 Electron 不支援此功能。 若要解決此問題，您可以嘗試使用裝置程式碼流程來進行登入。 若要這樣做，完成下列步驟：

1. 下拉式功能表預覽-> 「使用裝置程式碼登入」。
2. 開啟 [連線] 對話方塊 (透過左側垂直列上的插頭圖示，或透過在 [帳戶] 面板上的 [新增帳戶])。
3. 選擇您想要登入的環境。
4. 按一下 [登入] 按鈕。
5. 遵循下一個面板上的指示。

如果您發現登入您想要使用的帳戶時發生問題，因為您的預設瀏覽器已登入不同的帳戶，您可以：

1. 手動將連結和程式碼複製到您瀏覽器的隱私工作階段。
2. 手動將連結和程式碼複製到其他瀏覽器。

### <a name="reauthentication-loop-or-upn-change"></a>重新驗證迴圈或 UPN 變更

如果您是在重新驗證迴圈中，或已變更其中一個帳戶的 UPN，請嘗試下列步驟：

1. 移除所有的帳戶，然後關閉 [儲存體總管]
2. 從您的機器中刪除 .IdentityService 資料夾。 在 Windows 中，該資料夾位於 `C:\users\<username>\AppData\Local`。 對於 Mac 和 Linux，您可以在使用者目錄的根目錄中找到此資料夾。
3. 如果您是在 Mac 或 Linux 上，您也必須從作業系統的金鑰儲存區刪除 IdentityService 專案。 在 Mac 上，金鑰儲存區會是 "Gnome Keychain" 應用程式。 針對 Linux，此應用程式通常稱為 "Keyring"，但此名稱可能會因為您的散發版本不同而有差異。

### <a name="conditional-access"></a>條件式存取

當儲存體總管在 Windows 10、Linux 或 macOS 上使用時，不支援條件式存取。 這是因為儲存體總管所使用的 AAD 程式庫中有一項限制。

## <a name="mac-keychain-errors"></a>Mac Keychain 錯誤

macOS 鑰匙圈有時會進入導致 [儲存體總管] 的驗證程式庫發生問題的狀態。 若要讓 keychain 超出此狀態，請嘗試下列步驟：

1. 關閉 [儲存體總管]。
2. 開啟鑰匙圈 (**cmd + 空格鍵**，鍵入 keychain，按 Enter)。
3. 選取 [登入] 鑰匙圈。
4. 按一下掛鎖圖示以鎖定鑰匙圈 (完成時，掛鎖會以動畫方式顯示到鎖定的位置，視您所開啟的應用程式而定，它可能需要幾秒鐘的時間)。

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 啟動儲存體總管。
6. 快顯視窗應該會隨即出現，並包含類似「服務中樞想要存取 keychain」的訊息。 當它發生時，請輸入您的 Mac 系統管理員帳戶密碼，然後按一下 [**一律允許**] （或 [**允許**] [如果**永遠不允許**]，則可
7. 嘗試登入。

### <a name="general-sign-in-troubleshooting-steps"></a>一般登入疑難排解步驟

* 如果您是在 macOS 上，而且登入視窗永遠不會出現在 [正在等待驗證 ...]對話方塊，然後嘗試[下列步驟](#mac-keychain-errors)
* 重新啟動儲存體總管
* 如果驗證視窗空白，請在關閉驗證對話方塊之前先等待至少一分鐘。
* 確認您的機器和儲存體總管都已正確設定 Proxy 和憑證設定。
* 如果您是在 Windows 上，而且能夠存取同一部電腦上的 Visual Studio 2019 並登入，請嘗試登入 Visual Studio 2019。 成功登入 Visual Studio 2019 之後，您就可以在 [帳戶] 面板中開啟儲存體總管並查看您的帳戶。

如果這些方法都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>訂用帳戶遺失和租用戶損毀

如果您在成功登入後無法取得您的訂用帳戶，請嘗試下列疑難排解方法：

* 確認您的帳戶可存取預期的訂用帳戶。 您可以登入您想要使用的 Azure 環境入口網站，以驗證您的存取權。
* 請確定您已使用正確的 Azure 環境（Azure、azure 中國世紀、Azure 德國、Azure 美國政府或自訂環境）登入。
* 如果您是在 proxy 後方，請確定您已正確設定儲存體總管 proxy。
* 嘗試移除再重新新增帳戶。
* 如果有 [詳細資訊] 連結，請查看並查看哪些錯誤訊息會回報給失敗的租使用者。 如果 you'ren't 確定要如何處理您所看到的錯誤訊息，則您可以[在 GitHub 上開啟問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-attached-account-or-storage-resource"></a>無法移除附加的帳戶或儲存體資源

如果您無法透過 UI 移除附加的帳戶或儲存體資源，您可以藉由刪除下列資料夾，手動刪除所有已連結的資源：

* Windows：`%AppData%/StorageExplorer`
* macOS：`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux：`~/.config/StorageExplorer`

> [!NOTE]
> 請先關閉儲存體總管，然後再刪除上述資料夾。

> [!NOTE]
> 如果您曾經匯入任何 SSL 憑證，請備份 `certs` 目錄的內容。 稍後，您可以使用備份來重新匯入 SSL 憑證。

## <a name="proxy-issues"></a>Proxy 問題

首先，確定您輸入的下列資訊都正確：

* Proxy URL 和連接埠號碼
* 使用者名稱和密碼 (如果 proxy 要求)

> [!NOTE]
> 儲存體總管不支援 proxy 自動設定檔案來進行 proxy 設定。

### <a name="common-solutions"></a>常見的解決方案

如果您仍然遇到問題，請嘗試下列疑難排解方法：

* 如果可以不使用 proxy 連接到網際網路，請確認儲存體總管不啟用 proxy 設定也能運作。 如果是這種情況，可能是 proxy 設定發生問題。 請和您的 proxy 系統管理員合作找出問題。
* 確認使用 proxy 伺服器的其他應用程式是否如預期般運作。
* 確認您可以連線到您嘗試使用的 Azure 環境入口網站
* 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果可以連線，您應該會收到 InvalidQueryParameterValue 或類似的 XML 回應。
* 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果他們可以連線，您可能必須連絡您的 proxy 伺服器管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具（例如 Fiddler for Windows），您可以如下所示診斷問題：

* 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。
* 檢查網路工具使用的連接埠號碼。
* 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 如果作業正確，您的網路工具就會開始記錄儲存體總管向管理和服務端點提出的網路要求。 例如，在流覽 https://cawablobgrs.blob.core.windows.net/ 器中輸入 blob 端點，您會收到類似下面的回應，這表示資源存在，但您無法存取它。

![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果 proxy 設定正確，您可能要連絡您的 proxy 伺服器管理員，並

* 請確定您的 proxy 不會封鎖對 Azure 管理或資源端點的流量。
* 確認 proxy 伺服器使用的驗證通訊協定。 儲存體總管目前不支援 NTLM proxy。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果您是透過 proxy 連線到 Azure，請確認您的 proxy 設定正確無誤。 如果您已授與訂用帳戶或帳戶擁有者對資源的存取權，請確認您具有該資源的 [讀取] 或 [列出] 許可權。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>連接字串沒有完整的配置設定

如果您收到此錯誤訊息，可能是因為您沒有取得儲存體帳戶金鑰的必要許可權。 若要確認是否為此情形，請移至入口網站並找到您的儲存體帳戶。 以滑鼠右鍵按一下儲存體帳戶的節點，然後按一下 [在入口網站中開啟]，即可快速完成此動作。 這麼做之後，會移至 [存取金鑰] 刀鋒視窗。 如果您沒有視圖金鑰的許可權，則會看到包含「您沒有存取權」訊息的頁面。 若要解決此問題，您可以從其他人取得帳戶金鑰，並使用名稱和金鑰進行附加，或者您可以要求其他人提供儲存體帳戶的 SAS，並使用它來附加儲存體帳戶。

如果您看到帳戶金鑰，請在 GitHub 上提出問題，讓我們可以協助您解決問題。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>加入新連接時發生錯誤：TypeError無法讀取未定義的屬性 ' version '

如果您在嘗試新增自訂連線時收到此錯誤訊息，則儲存在本機認證管理員中的連接資料可能已損毀。
若要解決此問題，您可以嘗試刪除已損毀的本機連線，然後重新加入它們。

1. 啟動儲存體總管。 在頂端功能表中，移至 [說明] → [切換開發人員工具]。
2. 在開啟的視窗中，移至 [應用程式] 索引標籤→ [本機儲存體] （左側）→ [file://]
3. 視您遇到的連線類型而定，尋找其金鑰，並將其值複製到文字編輯器。 值是自訂連接名稱的陣列。
    * 儲存體帳戶
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Blob 容器
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * 檔案共用
        * `StorageExplorer_CustomConnections_Files_v1`
    * 佇列
        * `StorageExplorer_CustomConnections_Queues_v1`
    * 資料表
        * `StorageExplorer_CustomConnections_Tables_v1`
4. 儲存目前的連接名稱之後，將開發人員工具中的值設定為`[]`。

如果您想要保留未損毀的連接，您可以執行下列步驟來找出損毀的連線。 如果您不介意遺失所有現有的連線，您可以略過下列步驟，並遵循平臺特定的指示來清除您的連接資料。

1. 在文字編輯器中，將每個連接名稱重新加入至開發人員工具，並檢查連接是否仍在運作中。
2. 如果連線正常運作，則不會損毀，而且您可以放心地將它保留在該處。 如果連線無法運作，請將其值從開發人員工具中移除並加以記錄，讓您可以稍後再將它加入。
3. 重複執行，直到您檢查過所有連線為止。

完成所有的連線之後，針對所有未新增的連線名稱，您必須清除其損毀的資料（如果有的話），然後使用儲存體總管透過一般步驟將其重新加入。

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. 開啟 [認證管理員]，方法是開啟 [開始] 功能表並搜尋 [認證管理員]。
2. 在開啟的視窗中，移至 [Windows 認證]。
3. 在 [一般認證] 下，尋找具有索引`<connection_type_key>/<corrupted_connection_name>`鍵的專案（ `StorageExplorer_CustomConnections_Accounts_v1/account1`例如）。
4. 請移除這些專案，然後再重新加入連接。

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. 開啟 [焦點] （命令空間列），然後搜尋 [Keychain 存取]。
2. 尋找具有索引鍵`<connection_type_key>/<corrupted_connection_name>`的專案（例如， `StorageExplorer_CustomConnections_Accounts_v1/account1`）。
3. 刪除這些專案，然後再重新加入連接。

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

本機認證管理會根據 Linux 散發套件而有所不同。 如果您的 Linux 散發套件未提供內建的 GUI 工具來管理本機認證，您可以安裝協力廠商工具來管理本機認證。 例如，您可以使用[Seahorse](https://wiki.gnome.org/Apps/Seahorse/)，這是用來管理 Linux 本機認證的開放原始碼 GUI 工具。

1. 開啟您的本機認證管理工具，尋找您已儲存的認證。
2. 尋找具有索引鍵`<connection_type_key>/<corrupted_connection_name>`的專案（例如， `StorageExplorer_CustomConnections_Accounts_v1/account1`）。
3. 刪除這些專案，然後再重新加入連接。

如果您在執行這些步驟之後仍然遇到此錯誤，或者您想要共用您認為會損毀的連線，請在 GitHub 頁面上[開啟問題](https://github.com/microsoft/AzureStorageExplorer/issues)。

## <a name="issues-with-sas-url"></a>SAS URL 問題

如果您使用 SAS URL 連線到服務，而碰到此錯誤：

* 請確認 URL 提供讀取或列出資源的必要權限。
* 請確認 URL 尚未過期。
* 如果 SAS URL 是以存取原則為基礎，請確認尚未撤銷存取原則。

如果您不慎以無效的 SAS URL 進行連結，但是無法中斷連結，請遵循下列步驟：

1. 執行儲存體總管時，按下 F12 以開啟開發人員工具視窗。
2. 按一下 [應用程式] 索引標籤，然後在左邊樹狀目錄中，按一下 [本機儲存體] > file://。
3. 尋找與有問題的 SAS URI 服務類型相關聯的索引鍵。 例如，如果是 Blob 容器的 SAS URI 不正確，請尋找名為 `StorageExplorer_AddStorageServiceSAS_v1_blob` 的索引鍵。
4. 索引鍵的值應該是 JSON 陣列。 尋找與不正確 URI 相關聯的物件，並將它移除。
5. 按下 Ctrl+R，重新載入儲存體總管。

## <a name="linux-dependencies"></a>Linux 相依項目

儲存體總管1.10.0 和更新版本可作為貼齊存放區的貼齊。 [儲存體總管] 嵌入式管理單元會自動安裝其所有相依性，並在有新版本的嵌入式管理單元時進行更新。 安裝儲存體總管 嵌入式管理單元是建議的安裝方法。

儲存體總管需要使用密碼管理員，這可能需要手動連接，儲存體總管才能正常運作。 您可以使用下列命令，將儲存體總管連接到系統的密碼管理員：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

您也可以將應用程式下載為 gz 檔案，但必須手動安裝相依性。

> [!IMPORTANT]
> 只有 Ubuntu 發行版本才支援 gz 下載中提供的儲存體總管。 其他發行版本尚未經過驗證，可能需要其他套件或其他封裝。

這些套件是 Linux 上儲存體總管最常見的需求：

* [.NET Core 2.0 執行時間](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` 或 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> 儲存體總管版本1.7.0 和舊版需要 .NET Core 2.0。 如果您已安裝較新版本的 .NET Core，就必須[修補儲存體總管](#patching-storage-explorer-for-newer-versions-of-net-core)。 如果您執行的是儲存體總管1.8.0 或更高版本，則您應該能夠使用最多 .NET Core 2.2。 2\.2 以外的版本尚未經過驗證，無法在此時間使用。

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. 下載儲存體總管。
2. 安裝[.Net Core 運行](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)時間。
3. 執行下列命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下載儲存體總管。
2. 安裝[.Net Core 運行](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)時間。
3. 執行下列命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下載儲存體總管
2. 安裝[.Net Core 運行](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)時間。
3. 執行下列命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. 下載儲存體總管
2. 安裝[.Net Core 運行](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)時間。
3. 執行下列命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>修補較新版本 .NET Core 的儲存體總管

針對儲存體總管1.7.0 或更舊版本，您可能需要修補儲存體總管所使用的 .NET Core 版本。

1. [從 nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)下載 StreamJsonRpc 版本1.5.43。 尋找頁面右手邊的 [下載套件] 連結。
2. 下載套件之後，請將其副檔名從`.nupkg`變更為。 `.zip`
3. 將封裝解壓縮。
4. 開啟 `streamjsonrpc.1.5.43/lib/netstandard1.1/` 資料夾。
5. 複製`StreamJsonRpc.dll`到儲存體總管資料夾內的下列位置：
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>從 Azure 入口網站在 Explorer 中開啟無法使用

如果 Azure 入口網站的 [在 Explorer 中開啟] 按鈕無法供您使用，請確定您使用的是相容的瀏覽器。 已測試過下列瀏覽器的相容性。
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>後續步驟

如果這些解決方法對您都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。 您也可以使用左下角的 [回報問題到 GitHub] 按鈕，以快速前往 GitHub。

![意見](./media/storage-explorer-troubleshooting/feedback-button.PNG)
