---
title: 設定客戶管理的金鑰，從 Azure 入口網站的 Azure 儲存體加密
description: 了解如何使用 Azure 入口網站設定 Azure 儲存體加密的客戶管理金鑰。 客戶管理的金鑰可讓您建立、 輪替、 停用和撤銷的存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: baabc5a8e1d063cb51a3edea3a7218591e85aa1a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154162"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>設定客戶管理的金鑰，從 Azure 入口網站的 Azure 儲存體加密

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用客戶管理的金鑰，使用設定金鑰保存庫[Azure 入口網站](https://portal.azure.com/)。 若要了解如何建立金鑰保存庫使用 Azure 入口網站，請參閱[快速入門：設定並從使用 Azure 入口網站的 Azure 金鑰保存庫擷取祕密](../../key-vault/quick-create-portal.md)。 


> [!IMPORTANT]
> 使用客戶管理的金鑰使用 Azure 儲存體加密需要金鑰保存庫有兩個必要的屬性設定，**虛刪除**並**不要清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫，則預設會啟用這些屬性。 不過，如果您要啟用現有的金鑰保存庫上的這些屬性時，您必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

若要啟用客戶管理的金鑰，在 Azure 入口網站中，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定] 刀鋒視窗上，按一下 [加密]。 選取 [使用您自己的金鑰] 選項，如下圖所示。

    ![入口網站的螢幕擷取畫面顯示 [加密] 選項](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>指定索引鍵

啟用客戶管理的金鑰之後，您必須有機會指定儲存體帳戶相關聯的索引鍵。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

若要指定索引鍵為 URI，請遵循下列步驟：

1. 若要在 Azure 入口網站中尋找金鑰 URI，請瀏覽至金鑰保存庫，然後選取**金鑰**設定。 選取所需的金鑰，然後按一下要檢視其設定的索引鍵。 複製的值**金鑰識別碼**欄位，以提供 URI。

    ![螢幕擷取畫面顯示金鑰保存庫金鑰 URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. 在 **加密**設定為您的儲存體帳戶，選擇**輸入金鑰 URI**選項。
1. 在 [金鑰 URI] 欄位中，指定 URI。

   ![螢幕擷取畫面顯示如何輸入金鑰 URI](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

若要指定金鑰保存庫的金鑰，請先確定您有金鑰保存庫，包含索引鍵。 若要指定從 key vault 的金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取] 選項。
2. 選擇包含您想要使用之金鑰的金鑰保存庫。
3. 從金鑰保存庫選擇金鑰。

   ![顯示客戶管理金鑰的選項螢幕擷取畫面](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>更新金鑰的版本

當您建立新版本的金鑰時，您將需要更新儲存體帳戶，以使用新的版本。 請遵循下列步驟：

1. 瀏覽至您的儲存體帳戶，並顯示**加密**設定。
1. 指定新的金鑰版本的 URI。 或者，您可以選取金鑰保存庫以及金鑰一次更新的版本。

## <a name="next-steps"></a>後續步驟

- [待用資料的 azure 儲存體加密](storage-service-encryption.md)
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)？
