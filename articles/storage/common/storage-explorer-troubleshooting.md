---
title: Azure 儲存體總管疑難排解指南 | Microsoft Docs
description: Azure 存储资源管理器调试方法概述
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3e26365c4273611c81682a760695522575f3875d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483336"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 存储资源管理器故障排除指南

Microsoft Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 應用程式可以連線至裝載於 Azure、National Clouds 和 Azure Stack 上的儲存體帳戶。

本指南摘要說明儲存體總管中常見的問題解決方案。

## <a name="role-based-access-control-permission-issues"></a>基于角色的访问控制权限问题

[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 是通过将权限集组合成“角色”，来提供对 Azure 资源的精细访问管理。 可遵循以下建议在存储资源管理器中正常运行 RBAC。

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>需要满足哪些条件才能在存储资源管理器中查看我的资源？

如果在使用 RBAC 访问存储资源时遇到问题，原因可能是你尚未获得相应的角色。 以下部分介绍了存储资源管理器目前要求提供哪些权限来访问存储资源。

如果你不确定自己是否拥有相应的角色或权限，请联系 Azure 帐户管理员。

#### <a name="read-listget-storage-accounts"></a>讀取：列出/获取存储帐户

必须有权列出存储帐户。 可以请求分配“读取者”角色来获取此权限。

#### <a name="list-storage-account-keys"></a>列出存储帐户密钥

存储资源管理器还可以使用帐户密钥对请求进行身份验证。 可以使用权限更高的角色（例如“参与者”角色）来获取密钥的访问权限。

> [!NOTE]
> 访问密钥向其任何持有者授予不受限制的权限。 因此，通常不建议将其分发给帐户用户。 如果需要撤消访问密钥，可以通过 [Azure 门户](https://portal.azure.com/)重新生成访问密钥。

#### <a name="data-roles"></a>数据角色

必须至少拥有一个可以授予对资源中数据的读取访问权限的角色。 例如，如果需要列出或下载 Blob，则至少需要拥有“存储 Blob 数据读取者”角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>为何需要管理层角色才能在存储资源管理器中查看我的资源？

Azure 存储提供两个访问层：“管理”和“数据”。 订阅和存储帐户是通过管理层访问的。 容器、Blob 和其他数据资源是通过数据层访问的。 例如，若要从 Azure 获取存储帐户的列表，应向管理终结点发送请求。 若要列出帐户中的 Blob 容器，应向相应的服务终结点发送请求。

RBAC 角色可以包含对管理或数据访问层的权限。 例如，“读取者”角色授予对管理层资源的只读访问权限。

严格地讲，“读取者”角色不提供数据层的权限，并非一定要有该角色才能访问数据层。

在存储资源管理器中，可以通过收集连接到 Azure 资源所需的信息，来轻松访问资源。 例如，若要显示 Blob 容器，存储资源管理器会向 Blob 服务终结点发送“列出容器”请求。 若要获取该终结点，存储资源管理器会搜索你有权访问的订阅和存储帐户列表。 但是，若要查找订阅和存储帐户，存储资源管理器还需要有权访问管理层。

如果您還沒有授與任何管理層級權限的角色，儲存體總管無法取得所需連接到資料層的資訊。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我无法从管理员获取管理层权限，该怎么办？

目前，我们尚未制定 RBAC 相关的解决方法。 一种解决方法是请求一个 SAS URI 并将其[附加到资源](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas)。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error:憑證鏈結中的自我簽署憑證 (和類似錯誤)

憑證錯誤是由下列兩種情況的其中一個所造成：

1. 應用程式是透過「透明 proxy」連線，這表示伺服器 (例如您公司的伺服器) 是使用自我簽署憑證攔截 HTTPS 流量、解密再加密。
2. 您正在執行的應用程式，會將自我簽署的 SSL 憑證插入您收到的 HTTPS 訊息中。 會插入憑證的應用程式例子包括防毒軟體和網路流量檢查軟體。

當儲存體總管看見自我簽署或不受信任的憑證，就無法再得知收到的 HTTPS 訊息是否已被更改。 如果您有一份自我簽署憑證，便可以執行下列步驟，指示儲存體總管信任該憑證：

1. 取得憑證的 Base-64 編碼 X.509 (.cer) 複本
2. 按一下 [編輯] > [SSL 憑證] > [匯入憑證]，然後使用檔案選擇器來尋找、選取及開啟 .cer 檔案

此問題也可能是因為有多個憑證 (根憑證與中繼憑證)。 必須新增這兩個憑證，才能解決錯誤。

如果您不確定憑證來自何處，可以嘗試下列步驟找出憑證：

1. 安裝 Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版足可應對)
    * Mac 和 Linux：應該包含在作業系統中
2. 執行 Open SSL
    * Windows：開啟安裝目錄，按一下 **/bin/**，再按兩下 **openssl.exe**。
    * Mac 和 Linux：從終端機執行 **openssl**。
3. 執行 `s_client -showcerts -connect microsoft.com:443`
4. 尋找自我簽署憑證。 如果不确定哪些证书是自签名证书，请查看使用者 `("s:")` 和证书颁发者 `("i:")` 相同的任意位置。
5. 發現任何自我簽署的憑證時，請將每個憑證從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。
6. 開啟儲存體總管，按一下 [編輯] > [SSL 憑證] > [匯入憑證]，然後使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果使用上述步驟找不到任何自我簽署的憑證，請透過意見反應工具與我們連絡，以取得更多協助。 或者，您可以選擇從命令列使用 `--ignore-certificate-errors` 旗標來啟動儲存體總管。 使用這個旗標啟動時，儲存體總管會忽略憑證錯誤。

## <a name="sign-in-issues"></a>登入問題

### <a name="blank-sign-in-dialog"></a>空白登录对话框

出现空白登录对话框的原因往往是 ADFS 要求存储资源管理器执行 Electron 不支持的重定向。 若要解決此問題，您可以嘗試使用裝置程式碼流程來登入。 若要進行，請執行下列步驟：

1. “转到预览”->“使用设备代码登录”。
2. 開啟 [連線] 對話方塊 (透過左側垂直列上的插頭圖示，或透過在 [帳戶] 面板上的 [新增帳戶])。
3. 选择要登录到的环境。
4. 按一下 [登入] 按鈕。
5. 遵循下一個面板上的指示。

如果在登录到要使用的帐户时，由于默认浏览器已登录到其他帐户而遇到问题，可通过以下方法解决：

1. 手動將連結和程式碼複製到您瀏覽器的隱私工作階段。
2. 手動將連結和程式碼複製到其他瀏覽器。

### <a name="reauthentication-loop-or-upn-change"></a>重新驗證迴圈或 UPN 變更

如果您處於重新驗證的迴圈中，或已變更其中一個帳戶的 UPN，請嘗試下列方法：

1. 移除所有的帳戶，然後關閉 [儲存體總管]
2. 從您的機器中刪除 .IdentityService 資料夾。 在 Windows 中，該資料夾位於 `C:\users\<username>\AppData\Local`。 對於 Mac 和 Linux，您可以在使用者目錄的根目錄中找到此資料夾。
3. 如果您使用 Mac 或 Linux，您也必須從您的作業系統金鑰儲存區中刪除 Microsoft.Developer.IdentityService 項目。 在 Mac 上，金鑰儲存區會是 "Gnome Keychain" 應用程式。 針對 Linux，此應用程式通常稱為 "Keyring"，但此名稱可能會因為您的散發版本不同而有差異。

### <a name="conditional-access"></a>條件式存取

在 Windows 10、Linux 或 macOS 上使用儲存體總管時，不支援條件式存取。 這是因為儲存體總管所使用之 AAD 程式庫中的限制。

## <a name="mac-keychain-errors"></a>Mac Keychain 錯誤

macOS 鑰匙圈有時會進入導致 [儲存體總管] 的驗證程式庫發生問題的狀態。 若要使密钥链摆脱此状态，请尝试以下步骤：

1. 關閉 [儲存體總管]。
2. 開啟鑰匙圈 (**cmd + 空格鍵**，鍵入 keychain，按 Enter)。
3. 選取 [登入] 鑰匙圈。
4. 按一下掛鎖圖示以鎖定鑰匙圈 (完成時，掛鎖會以動畫方式顯示到鎖定的位置，視您所開啟的應用程式而定，它可能需要幾秒鐘的時間)。

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 啟動儲存體總管。
6. 快顯視窗應該會隨即出現，並包含類似「服務中樞想要存取 keychain」的訊息。 若有出現此快顯視窗，請輸入您的 Mac 系統管理員帳戶密碼，並按一下 [一律允許] (或是，如果沒有 [一律允許]，則按一下 [允許])。
7. 嘗試登入。

### <a name="general-sign-in-troubleshooting-steps"></a>一般登入疑難排解步驟

* 在 macOS 上操作时，如果登录窗口永远不会出现，而是一直显示“正在等待身份验证...”对话框，请尝试[这些步骤](#mac-keychain-errors)
* 重新啟動儲存體總管
* 如果驗證視窗空白，請在關閉驗證對話方塊之前先等待至少一分鐘。
* 確認您的機器和儲存體總管都已正確設定 Proxy 和憑證設定。
* 如果在 Windows 上操作，并且有权访问同一台计算机上的 Visual Studio 2017 且可以登录，请尝试登录到 Visual Studio 2017。 成功登入 Visual Studio 2017 之後，您應該能夠開啟 [儲存體總管]，並在 [帳戶] 面板中查看您的帳戶。

如果這些方法都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>訂用帳戶遺失和租用戶損毀

如果成功登入後無法擷取您的訂用帳戶，請嘗試下列疑難排解方法：

* 確認您的帳戶可存取預期的訂用帳戶。 可以通过登录到尝试使用的 Azure 环境的门户，来验证是否能够访问这些订阅。
* 確定已使用正確的 Azure 環境 (Azure、Azure 中國 21Vianet、Azure 德國、Azure 美國政府或自訂環境) 來登入。
* 如果您是在 proxy 背景，請確定已正確設定儲存體總管的 proxy。
* 嘗試移除再重新新增帳戶。
* 如果有 [詳細資訊] 連結，請查看針對失敗租用戶回報的錯誤訊息內容。 如果您不確定該如何處理看到的錯誤訊息，歡迎您在 [GitHub 上提出問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cannot-remove-attached-account-or-storage-resource"></a>無法移除連結的帳戶或儲存體資源

如果您透過使用者介面無法移除連結的帳戶或儲存體資源，則可以刪除下列資料夾，以手動刪除連結的所有資源：

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
> 存储资源管理器不支持使用代理 auto-config 文件来配置代理设置。

### <a name="common-solutions"></a>常見的解決方案

如果您仍然遇到問題，請嘗試下列疑難排解方法：

* 如果可以不使用 proxy 連接到網際網路，請確認儲存體總管不啟用 proxy 設定也能運作。 如果是這種情況，可能是 proxy 設定發生問題。 請和您的 proxy 系統管理員合作找出問題。
* 確認使用 proxy 伺服器的其他應用程式是否如預期般運作。
* 確認您可以連線至想要使用的 Azure 環境入口網站
* 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果可以連線，您應該會收到 InvalidQueryParameterValue 或類似的 XML 回應。
* 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果他們可以連線，您可能必須連絡您的 proxy 伺服器管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具，例如 Fiddler for Windows，您可以診斷問題，如下所示：

* 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。
* 檢查網路工具使用的連接埠號碼。
* 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 如果作業正確，您的網路工具就會開始記錄儲存體總管向管理和服務端點提出的網路要求。 例如，在瀏覽器中輸入 blob 端點 https://cawablobgrs.blob.core.windows.net/，便會收到類似下面的回應，代表雖然您無法存取，但資源確實存在。

![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果 proxy 設定正確，您可能要連絡您的 proxy 伺服器管理員，並

* 確定 proxy 不會封鎖 Azure 管理或資源端點的流量。
* 確認 proxy 伺服器使用的驗證通訊協定。 儲存體總管目前不支援 NTLM proxy。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果透過 proxy 連線至 Azure，請確認您的 proxy 設定正確無誤。 如果已获取以订阅或帐户所有者身份访问资源的权限，请验证是否对该资源拥有读取或列出权限。

## <a name="connection-string-does-not-have-complete-configuration-settings"></a>連接字串沒有完整的組態設定

如果您收到此錯誤訊息，很可能您沒有取得儲存體帳戶金鑰所需的權限。 若要確認是否為此情形，請移至入口網站並找到您的儲存體帳戶。 可以通过右键单击存储帐户的节点并单击“在门户中打开”来快速执行此操作。 這麼做之後，會移至 [存取金鑰] 刀鋒視窗。 如果你无权查看密钥，则会看到其中显示了消息“你没有访问权限”的页面。 若要解决此问题，可以从其他某人获取帐户密钥并结合名称和密钥附加存储帐户，或者，向某人索要存储帐户的 SAS 并使用它来附加存储帐户。

如果看到了帐户密钥，请在 GitHub 上提出问题，使我们能够帮助你解决问题。

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

一般情况下，需要安装以下包才能在 Linux 上运行存储资源管理器：

* [.NET core 2.0 執行階段](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgnome-keyring-common`和`libgnome-keyring-dev`
* `libgconf-2-4`

根据所用的分发版，可能还需要安装不同的包或其他包。

Ubuntu 18.04、16.04 和 14.04 正式支持存储资源管理器。 全新计算机上的安装步骤如下：

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下载存储资源管理器
2. 安装 .NET Core 运行时，已验证的最新版本为：[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8)（如果已安装更高的版本，可能需要修补存储资源管理器，请参阅下文）
3. 執行 `sudo apt-get install libgconf-2-4`
4. 執行 `sudo apt install libgnome-keyring-common libgnome-keyring-dev`

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下载存储资源管理器
2. 安装 .NET Core 运行时，已验证的最新版本为：[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8)（如果已安装更高的版本，可能需要修补存储资源管理器，请参阅下文）
3. 執行 `sudo apt install libgnome-keyring-dev`

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. 下载存储资源管理器
2. 安装 .NET Core 运行时，已验证的最新版本为：[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8)（如果已安装更高的版本，可能需要修补存储资源管理器，请参阅下文）
3. 執行 `sudo apt install libgnome-keyring-dev`

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>修补适用于 .NET Core 更高版本的存储资源管理器 
如果安装的 .NET Core 版本高于 2.0，而运行的存储资源管理器版本为 1.7.0 或更低，则很有可能需要通过完成以下步骤来修补存储资源管理器：
1. [从 Nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43) 中下载 StreamJsonRpc 版本 1.5.43。 在页面的右侧找到“下载包”链接。
2. 下載封裝之後, 變更其副檔名從`.nupkg`至 `.zip`
3. 将包解压缩
4. 移至 `streamjsonrpc.1.5.43/lib/netstandard1.1/`。
5. 将 `StreamJsonRpc.dll` 复制到存储资源管理器文件夹中的以下位置：
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Azure 门户中的“在资源管理器中打开”不起作用

如果在 Azure 门户上单击“在资源管理器中打开”按钮不起作用，请确保使用兼容的浏览器。 以下浏览器已通过兼容性测试。
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>後續步驟

如果這些解決方法對您都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。 您也可以使用左下角的 [回報問題到 GitHub] 按鈕，以快速前往 GitHub。

![意見反應](./media/storage-explorer-troubleshooting/feedback-button.PNG)
