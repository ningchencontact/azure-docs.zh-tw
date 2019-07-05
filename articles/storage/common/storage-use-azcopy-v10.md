---
title: 複製或移動資料至 Azure 儲存體，使用 AzCopy v10 |Microsoft Docs
description: AzCopy 是命令列公用程式可供您至、 或儲存體帳戶之間複製資料。 這篇文章可協助您下載 AzCopy，連接到儲存體帳戶，然後將檔案傳送。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565793"
---
# <a name="get-started-with-azcopy"></a>開始使用 AzCopy

AzCopy 是命令列公用程式可供您儲存體帳戶來回複製 blob 或檔案。 這篇文章可協助您下載 AzCopy，連接到儲存體帳戶，然後將檔案傳送。

> [!NOTE]
> AzCopy **V10**是目前支援的版本的 AzCopy。
>
> 如果您要使用 AzCopy **v8.1**，請參閱[使用舊版的 AzCopy](#previous-version)一節。

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>下載 AzCopy

首先，下載到您的電腦上的任何目錄的 AzCopy V10 可執行檔。 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> 如果您想要複製資料，與您[Azure 表格儲存體](https://docs.microsoft.com/azure/storage/tables/table-storage-overview)服務，然後安裝[AzCopy 版本 7.3](https://aka.ms/downloadazcopynet)。

## <a name="run-azcopy"></a>執行 AzCopy

為了方便起見，請考慮將 AzCopy 可執行檔的目錄位置新增至您的系統路徑，以方便使用。 如此一來您可以輸入`azcopy`從您的系統上的任何目錄。

如果您選擇不要將 AzCopy 目錄新增至您的路徑，您必須將目錄變更至 AzCopy 可執行檔和類型的位置`azcopy`或`.\azcopy`在 Windows PowerShell 命令提示字元。

若要查看命令的清單，請輸入`azcopy -h`然後按下 ENTER 鍵。

若要深入了解特定命令，只包含命令的名稱 (例如： `azcopy list -h`)。

![內嵌說明](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> 為您的 Azure 儲存體帳戶的擁有者，您不會自動指派資料存取權限。 您可以執行使用 AzCopy 有意義的任何項目之前，您必須決定如何將提供的儲存體服務的授權認證。 

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

您可以使用 Azure Active Directory (AD)，或使用共用存取簽章 (SAS) 權杖來提供授權認證。

您可以使用下表作為指南：

| 儲存體類型 | 授權的目前支援的方法 |
|--|--|
|**Blob 儲存體** | Azure AD & SAS |
|**Blob 儲存體 （階層式命名空間）** | Azure AD & SAS |
|**檔案儲存體** | 只有 SAS |

### <a name="option-1-use-azure-ad"></a>選項 1：使用 Azure AD

使用 Azure AD，您可以提供一次而不需將 SAS 權杖附加至每個命令的認證。  

您需要的授權層級取決於您是否計劃將檔案上傳或下載它們。

如果您只想下載檔案，然後確認[儲存體 Blob 資料讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)已指派給您的使用者身分識別或服務主體。 

> [!NOTE]
> 使用者的身分識別和服務主體是每一種*安全性主體*，因此我們將使用的詞彙*安全性主體*這篇文章的其餘部分。

如果您想要上傳檔案，然後確認，這些角色的其中一個已指派給安全性主體：

- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以指派給您在任何這些領域中的身分識別：

- 容器 （檔案系統）
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要了解如何驗證和指派角色，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

> [!NOTE] 
> 請記住，RBAC 角色指派可能需要五分鐘的時間傳播。

您不需要有其中一個角色指派給安全性主體，如果您的安全性主體新增至目標容器或目錄的存取控制清單 (ACL)。 在 ACL 中，您的安全性主體會需要寫入權限的目標目錄上，並執行容器和每個父目錄的權限。

若要進一步了解，請參閱[存取控制，在 Azure Data Lake 儲存體 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-a-user-identity"></a>驗證使用者身分識別

確認您的使用者身分識別具有已指定必要的授權層級之後，開啟命令提示字元，輸入下列命令，，然後按 ENTER 鍵。

```azcopy
azcopy login
```

如果您隸屬於多個組織，包括儲存體帳戶所屬的組織的租用戶識別碼。

```azcopy
azcopy login --tenant-id=<tenant-id>
```

取代`<tenant-id>`預留位置取代為儲存體帳戶所屬的組織的租用戶識別碼。 若要尋找的租用戶識別碼，請選取**Azure Active Directory > 屬性 > 目錄識別碼**在 Azure 入口網站中。

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]  按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>驗證服務主體

如果您打算執行而不需要使用者互動的指令碼內使用 AzCopy，這會是不錯的選擇。 

在執行該指令碼之前，您必須登入以互動方式在至少一次，讓您可以提供您的服務主體的認證中的 AzCopy。  這些認證會儲存在安全且加密的檔案中，如此您的指令碼不需要提供該項機密資訊。

您可以使用用戶端祕密，或使用服務主體的應用程式註冊相關聯憑證的密碼登入您的帳戶。 

若要深入了解建立服務主體，請參閱[How to:使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

若要深入了解服務主體在一般情況下，請參閱[應用程式和 Azure Active Directory 中的服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>使用用戶端祕密

先是設定`AZCOPY_SPA_CLIENT_SECRET`環境變數，將您的服務主體的用戶端密碼的應用程式註冊。 

> [!NOTE]
> 請務必將此值設定從命令提示字元，而不是在環境變數設定您的作業系統。 如此一來，值為僅適用於目前的工作階段。

這個範例會示範如何您無法在 PowerShell 中這麼。

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 請考慮使用提示字元，在此範例中所示。 如此一來，用戶端祕密不會出現在您的主控台命令歷程記錄。 

接下來，輸入下列命令，，，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --application-id <application-id>
```

取代`<application-id>`預留位置取代為您的服務主體的應用程式識別碼的應用程式註冊。

##### <a name="using-a-certificate"></a>使用憑證

如果您想要使用您自己的認證進行授權，您可以將憑證上傳到您的應用程式註冊，然後使用 登入該憑證。

除了您的憑證上傳至您的應用程式註冊，您也要有一份憑證儲存至電腦或 AzCopy 將會執行的 VM。 這份憑證應該位於中。PFX 或。PEM 格式，然後必須包含私密金鑰。 私用金鑰應受密碼保護。 如果您使用 Windows，而且您的憑證只存在於憑證存放區，請確定該憑證匯出至 PFX 檔案 （包括私密金鑰）。 如需指引，請參閱[Export-pfxcertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

接下來，設定`AZCOPY_SPA_CERT_PASSWORD`環境變數，將憑證密碼。

> [!NOTE]
> 請務必將此值設定從命令提示字元，而不是在環境變數設定您的作業系統。 如此一來，值為僅適用於目前的工作階段。

這個範例會示範如何您無法在 PowerShell 中這麼。

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

接下來，輸入下列命令，，，然後按 ENTER 鍵。

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

取代`<path-to-certificate-file>`預留位置取代為憑證檔案的相對或完整路徑。 AzCopy 將此憑證的路徑，但它不會儲存一份憑證，因此請務必將該憑證保留在原位。

> [!NOTE]
> 請考慮使用提示字元，在此範例中所示。 如此一來，您的密碼不會出現在您的主控台命令歷程記錄。 

### <a name="option-2-use-a-sas-token"></a>選項 2：使用 SAS 權杖

您可以在 AzCopy 命令中，將 SAS 權杖附加至使用每個來源或目的地 URL 中。

此範例命令以遞迴方式將資料從本機目錄複製到 blob 容器。 虛構的 SAS 權杖附加至結尾的容器 URL。

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

若要深入了解 SAS 權杖，以及如何取得憑證，請參閱[使用共用存取簽章 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="transfer-files"></a>非同步傳送檔案

您已驗證您的身分識別，或取得 SAS 權杖之後，您可以開始傳送檔案。

若要尋找的命令範例，請參閱這些文件。

- [使用 AzCopy 和 Blob 儲存體轉送資料](storage-use-azcopy-blobs.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [使用 AzCopy 和 Azure Stack 儲存體傳輸資料](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>在 指令碼中使用 AzCopy

在執行該指令碼之前，您必須登入以互動方式在至少一次，讓您可以提供您的服務主體的認證中的 AzCopy。  這些認證會儲存在安全且加密的檔案中，如此您的指令碼不需要提供該項機密資訊。 如需範例，請參閱[驗證您的服務主體](#service-principal)一節。

經過一段時間，AzCopy[下載連結](#download-and-install-azcopy)會指向新版本的 AzCopy。 如果您的指令碼下載 AzCopy、 指令碼可能會停止運作如果新版的 AzCopy 修改取決於您的指令碼的功能。 

若要避免這些問題，取得靜態的 （未變更） 連結至目前版本的 AzCopy。 如此一來，您的指令碼會下載相同的確切版本的 AzCopy 執行每一次。

若要取得連結，請執行此命令：

| 作業系統  | 命令 |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> 針對 Linux，`--strip-components=1`上`tar`命令會移除最上層的資料夾，其中包含版本名稱，並改為直接在目前的資料夾中擷取二進位檔。 這可讓新的版本更新的指令碼`azcopy`藉由只更新`wget`URL。

URL 會出現在這個命令的輸出。 您的指令碼可以使用該 URL，然後下載 AzCopy。

| 作業系統  | 命令 |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>使用 AzCopy 在儲存體總管

如果您想要利用 AzCopy 的效能優勢，但您想要使用儲存體總管，而不是命令列與檔案互動，然後讓 AzCopy 在儲存體總管。 

在儲存體總管 中，選擇**Preview**->**改善 Blob 上傳和下載使用 AzCopy**。

![為 Azure 儲存體總管中的傳輸引擎讓 AzCopy](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 您不需要啟用此設定，如果您已在您的儲存體帳戶上啟用階層式命名空間。 這是因為儲存體總管會自動使用 AzCopy 上有階層式命名空間的儲存體帳戶。  

儲存體總管會使用您的帳戶金鑰來執行作業，因此您登入儲存體總管之後，您不需要提供額外的授權認證。

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用舊版的 AzCopy

如果您需要使用舊版的 AzCopy (AzCopy v8.1)，請參閱下列連結：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、 最佳化和疑難排解 AzCopy

請參閱[設定，最佳化和疑難排解 AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>後續步驟

如果您有疑問、 問題或一般的意見反應，將它們提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy)頁面。
