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
ms.openlocfilehash: bfa3e5a943ee59b1ed335f45e113a60f62572675
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735021"
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

您可以執行使用 AzCopy 有意義的任何項目之前，您必須決定如何將提供的儲存體服務的授權認證。

## <a name="choose-how-youll-provide-authorization-credentials"></a>選擇您要如何提供授權認證

您可以使用 Azure Active Directory (AD)，或使用共用存取簽章 (SAS) 權杖來提供授權認證。

您可以使用下表作為指南：

| 儲存體類型 | 授權的目前支援的方法 |
|--|--|
|**Blob 儲存體** | Azure AD & SAS |
|**Blob 儲存體 （階層式命名空間）** | 僅限 Azure AD 使用 |
|**檔案儲存體** | 只有 SAS |

### <a name="option-1-use-azure-ad"></a>選項 1：使用 Azure AD

您需要的授權層級取決於您是否計劃將檔案上傳或下載它們。

#### <a name="authorization-to-upload-files"></a>若要將檔案上傳的授權

確認，以您的身分識別將其中一個角色指派：

- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以指派給您在任何這些領域中的身分識別：

- 容器 （檔案系統）
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要了解如何驗證和指派角色，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

您不需要有其中一個角色指派給您的身分識別，如果您的身分識別新增至目標容器或目錄的存取控制清單 (ACL)。 在 ACL 中，您的身分識別會需要寫入權限的目標目錄上，並執行容器和每個父目錄的權限。

若要進一步了解，請參閱[存取控制，在 Azure Data Lake 儲存體 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authorization-to-download-files"></a>若要下載檔案的授權

確認，以您的身分識別將其中一個角色指派：

- [儲存體 Blob 資料讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)
- [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

這些角色可以指派給您在任何這些領域中的身分識別：

- 容器 （檔案系統）
- 儲存體帳戶
- 資源群組
- 訂用帳戶

若要了解如何驗證和指派角色，請參閱[授與存取 Azure blob 和佇列資料使用 RBAC 在 Azure 入口網站中](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

您不需要有其中一個角色指派給您的身分識別，如果您的身分識別新增至目標容器或目錄的存取控制清單 (ACL)。 在 ACL 中，您的身分識別會需要目標目錄中，讀取權限，以及執行容器和每個父目錄的權限。

若要進一步了解，請參閱[存取控制，在 Azure Data Lake 儲存體 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

#### <a name="authenticate-your-identity"></a>驗證您的身分識別

確認您的身分識別具有已指定必要的授權層級之後，開啟命令提示字元，輸入下列命令，，然後按 ENTER 鍵。

```azcopy
azcopy login
```

此命令傳回驗證碼和網站的 URL。 開啟網站，提供程式碼，然後選擇 [下一步]  按鈕。

![建立容器](media/storage-use-azcopy-v10/azcopy-login.png)

隨即會出現登入視窗。 在該視窗中，使用您的 Azure 帳戶認證登入 Azure 帳戶。 順利登入之後，您可以關閉瀏覽器視窗，然後開始使用 AzCopy。

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

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>設定、 最佳化和疑難排解 AzCopy

請參閱[設定，最佳化和疑難排解 AzCopy](storage-use-azcopy-configure.md)

## <a name="use-azcopy-in-storage-explorer"></a>使用 AzCopy 在儲存體總管

如果您想要利用 AzCopy 的效能優勢，但您想要使用儲存體總管，而不是命令列與檔案互動，然後讓 AzCopy 在儲存體總管。

在儲存體總管 中，選擇**Preview**->**改善 Blob 上傳和下載使用 AzCopy**。

![為 Azure 儲存體總管中的傳輸引擎讓 AzCopy](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> 您不需要啟用此設定，如果您已在您的儲存體帳戶上啟用階層式命名空間。 這是因為儲存體總管會自動使用 AzCopy 上有階層式命名空間的儲存體帳戶。  

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>使用舊版的 AzCopy

如果您需要使用舊版的 AzCopy (AzCopy v8.1)，請參閱下列連結：

- [AzCopy on Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)
- [AzCopy on Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="next-steps"></a>後續步驟

如果您有疑問、 問題或一般的意見反應，將它們提交[GitHub 上](https://github.com/Azure/azure-storage-azcopy)頁面。
