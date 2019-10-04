---
title: 待用資料的 Azure 儲存體加密 |Microsoft Docs
description: Azure 儲存體保護您的資料，方法是在將它保存到雲端之前自動將其加密。 您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a15d450d033c04c59f6981a887689f1fc08919f1
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958860"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體加密

Azure 儲存體會在將資料保存到雲端時，自動將其加密。 加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 Azure 儲存體中的資料會使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)（可用的最強區塊密碼之一），以透明的方式進行加密和解密，且符合 FIPS 140-2 標準。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

已針對所有新的和現有的儲存體帳戶啟用 Azure 儲存體加密，且無法停用。 因為預設會保護您的資料，所以您不需要修改程式碼或應用程式，就能利用 Azure 儲存體加密。

無論儲存體帳戶的效能層級（標準或高階）或部署模型（Azure Resource Manager 或傳統），都會進行加密。 所有 Azure 儲存體的冗余選項都支援加密，而且儲存體帳戶的所有複本都會加密。 所有 Azure 儲存體資源都會加密，包括 blob、磁片、檔案、佇列和資料表。 所有物件中繼資料也會加密。

加密不會影響 Azure 儲存體效能。 Azure 儲存體加密不會產生額外的費用。

如需 Azure 儲存體加密之基礎密碼編譯模組的詳細資訊，請參閱 @no__t 0Cryptography API：新一代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)。

## <a name="about-encryption-key-management"></a>關於加密金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。 如果您選擇使用自己的金鑰來管理加密，您有兩個選項：

- 您可以指定*客戶管理的金鑰*，以用來加密和解密儲存體帳戶中的所有資料。 客戶管理的金鑰可用來加密儲存體帳戶中所有服務的所有資料。
- 您可以在 Blob 儲存體作業上指定*客戶提供的金鑰*。 對 Blob 儲存體發出讀取或寫入要求的用戶端可以在要求中包含加密金鑰，以便更精確地控制 blob 資料的加密和解密方式。

下表比較 Azure 儲存體加密的金鑰管理選項。

|                                        |    Microsoft 管理的金鑰                             |    客戶管理的金鑰                                                                                                                        |    客戶提供的金鑰                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    加密/解密作業    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    支援的 Azure 儲存體服務    |    全部                                                |    Blob 儲存體，Azure 檔案儲存體                                                                                                               |    Blob 儲存體                                                                  |
|    金鑰儲存                         |    Microsoft 金鑰存放區    |    Azure Key Vault                                                                                                                              |    Azure Key Vault 或任何其他金鑰存放區                                                                 |
|    金鑰輪替責任         |    Microsoft                                          |    客戶                                                                                                                                     |    客戶                                                                      |
|    金鑰使用方法                           |    Microsoft                                          |    Azure 入口網站，儲存體資源提供者 REST API，Azure 儲存體管理程式庫，PowerShell，CLI        |    Azure 儲存體 REST API （Blob 儲存體），Azure 儲存體用戶端程式庫    |
|    金鑰存取                          |    僅限 Microsoft                                     |    Microsoft、客戶                                                                                                                    |    僅限客戶                                                                 |

下列各節會更詳細地說明金鑰管理的每個選項。

## <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

根據預設，您的儲存體帳戶會使用 Microsoft 管理的加密金鑰。 您可以在[Azure 入口網站](https://portal.azure.com)的 [**加密**] 區段中看到儲存體帳戶的加密設定，如下圖所示。

![查看使用 Microsoft 管理的金鑰加密的帳戶](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>客戶管理的金鑰

您可以選擇使用自己的金鑰來管理儲存體帳戶層級的 Azure 儲存體加密。 當您在儲存體帳戶層級指定客戶管理的金鑰時，該金鑰會用來加密和解密儲存體帳戶中的所有資料，包括 blob、佇列、檔案和資料表資料。  客戶管理的金鑰提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

您必須使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](../../key-vault/key-vault-overview.md)。

下圖顯示 Azure 儲存體如何使用 Azure Active Directory 和 Azure Key Vault，以使用客戶管理的金鑰來提出要求：

![此圖顯示客戶管理的金鑰在 Azure 儲存體中的使用方式](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

下列清單說明圖表中編號的步驟：

1. Azure Key Vault 系統管理員會將加密金鑰的許可權授與儲存體帳戶相關聯的受控識別。
2. Azure 儲存體管理員會使用儲存體帳戶的客戶管理金鑰來設定加密。
3. Azure 儲存體會使用與儲存體帳戶相關聯的受控識別，透過 Azure Active Directory 來驗證 Azure Key Vault 的存取權。
4. Azure 儲存體會將帳戶加密金鑰包裝在 Azure Key Vault 中的客戶金鑰。
5. 對於讀取/寫入作業，Azure 儲存體會將要求傳送給 Azure Key Vault，以包裝和解除封裝帳戶加密金鑰，以執行加密和解密作業。

若要撤銷儲存體帳戶上客戶管理金鑰的存取權，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/)和[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對儲存體帳戶中所有資料的存取，因為 Azure 儲存體無法存取加密金鑰。

[Azure 受控磁片](../../virtual-machines/windows/managed-disks-overview.md)不支援客戶管理的金鑰。

若要瞭解如何搭配 Azure 儲存體使用客戶管理的金鑰，請參閱下列其中一篇文章：

- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 使用客戶管理的金鑰與 Azure 儲存體加密](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure Active Directory （Azure AD）的功能。 當您在 Azure 入口網站中設定客戶管理的金鑰時，受管理的身分識別會自動指派給您的儲存體帳戶。 如果您接著將訂用帳戶、資源群組或儲存體帳戶從一個 Azure AD 目錄移到另一個，則與儲存體帳戶相關聯的受控識別不會傳輸至新的租使用者，因此客戶管理的金鑰可能無法再使用。 如需詳細資訊，請參閱常見問題中的 Azure AD 目錄[和 Azure 資源的受控識別的已知問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中**的訂**用帳戶。  

## <a name="customer-provided-keys-preview"></a>客戶提供的金鑰（預覽）

對 Azure Blob 儲存體提出要求的用戶端，可以選擇在個別要求上提供加密金鑰。 在要求中包含加密金鑰，可讓您更精確地控制 Blob 儲存體作業的加密設定。 客戶提供的金鑰（預覽）可以儲存在 Azure Key Vault 或另一個金鑰存放區中。

### <a name="encrypting-read-and-write-operations"></a>加密讀取和寫入作業

當用戶端應用程式在要求上提供加密金鑰時，Azure 儲存體會在讀取和寫入 blob 資料時，以透明的方式執行加密和解密。 加密金鑰的 SHA-256 雜湊會連同 blob 的內容一起寫入，並用來驗證對 blob 的所有後續作業都使用相同的加密金鑰。 Azure 儲存體不會儲存或管理用戶端與要求一起傳送的加密金鑰。 加密或解密程式完成後，就會安全地捨棄金鑰。

當用戶端使用客戶提供的金鑰來建立或更新 blob 時，該 blob 的後續讀取和寫入要求也必須提供金鑰。 如果未在已使用客戶提供的金鑰加密之 blob 的要求上提供金鑰，則要求會失敗，錯誤碼為409（衝突）。

如果用戶端應用程式在要求上傳送加密金鑰，而且也使用 Microsoft 管理的金鑰或客戶管理的金鑰來加密儲存體帳戶，則 Azure 儲存體會使用要求中提供的金鑰進行加密和解密。

若要傳送加密金鑰做為要求的一部分，用戶端必須使用 HTTPS 建立與 Azure 儲存體的安全連線。

每個 blob 快照集都可以有自己的加密金鑰。

### <a name="request-headers-for-specifying-customer-provided-keys"></a>要求標頭，用於指定客戶提供的金鑰

針對 REST 呼叫，用戶端可以使用下列標頭，安全地將要求的加密金鑰資訊傳遞至 Blob 儲存體：

|要求標頭 | 描述 |
|---------------|-------------|
|`x-ms-encryption-key` |寫入和讀取要求都需要。 Base64 編碼的 AES-256 加密金鑰值。 |
|`x-ms-encryption-key-sha256`| 寫入和讀取要求都需要。 加密金鑰的 Base64 編碼 SHA256。 |
|`x-ms-encryption-algorithm` | 寫入要求的必要，對於讀取要求則為選擇性。 指定使用指定的索引鍵來加密資料時，所要使用的演算法。 必須是 AES256。 |

在要求上指定加密金鑰是選擇性的。 不過，如果您指定上面列出的其中一個標頭來進行寫入作業，則必須指定它們的全部。

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支援客戶提供之金鑰的 Blob 儲存體作業

下列 Blob 儲存體作業支援在要求上傳送客戶提供的加密金鑰：

- [Put Blob](/rest/api/storageservices/put-blob) \(英文\)
- [Put Block List](/rest/api/storageservices/put-block-list) \(英文\)
- [放置區塊](/rest/api/storageservices/put-block)
- [將區塊從 URL 放入](/rest/api/storageservices/put-block-from-url)
- [放置頁面](/rest/api/storageservices/put-page)
- [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url)
- [附加區塊](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties) \(英文\)
- [設定 Blob 中繼資料](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob](/rest/api/storageservices/get-blob)
- [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)
- [取得 Blob 中繼資料](/rest/api/storageservices/get-blob-metadata)
- [快照集 Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>輪替客戶提供的金鑰

若要輪替要求所傳遞的加密金鑰，請下載該 blob，然後使用新的加密金鑰重新上傳它。

> [!IMPORTANT]
> Azure 入口網站不能用來讀取或寫入使用要求所提供之金鑰加密的容器或 blob。
>
> 請務必保護您在 Azure Key Vault 的安全金鑰存放區中，對 Blob 儲存體的要求所提供的加密金鑰。 如果您嘗試在沒有加密金鑰的容器或 blob 上進行寫入作業，此操作將會失敗，而且您將無法存取物件。

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>範例：在 .NET 中使用客戶提供的金鑰來上傳 blob

下列範例會建立客戶提供的金鑰，並使用該金鑰來上傳 blob。 程式碼會上傳區塊，然後認可封鎖清單，將 blob 寫入 Azure 儲存體。 藉由設定[CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey)屬性，在[BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions)物件上提供索引鍵。

金鑰是使用[AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider)類別所建立。 若要在您的程式碼中建立這個類別的實例，請加入參考 `System.Security.Cryptography` 命名空間的 `using` 語句：

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure 儲存體加密與磁片加密

使用 Azure 儲存體加密，所有 Azure 儲存體帳戶及其包含的資源都會加密，包括備份 Azure 虛擬機器磁片的分頁 blob。 此外，您可以使用[Azure 磁碟加密](../../security/azure-security-disk-encryption-overview.md)來加密 Azure 虛擬機器磁片。 Azure 磁碟加密在 Windows 上使用業界標準的[BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) ，而在 Linux 上則為[DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) ，以提供與 Azure Key Vault 整合之以作業系統為基礎的加密解決方案。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md)
- [從 Azure 入口網站設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-portal.md)
- [從 PowerShell 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-powershell.md)
- [從 Azure CLI 設定客戶管理金鑰以進行 Azure 儲存體加密](storage-encryption-keys-cli.md)
