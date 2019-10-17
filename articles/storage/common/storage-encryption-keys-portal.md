---
title: 針對來自 Azure 入口網站的 Azure 儲存體加密設定客戶管理的金鑰
description: 瞭解如何使用 Azure 入口網站來設定 Azure 儲存體加密的客戶管理金鑰。 客戶管理的金鑰可讓您建立、輪替、停用及撤銷存取控制。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 74ca6f15baeeb9fe8adad4bda80e313a4b4cf03a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376231"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>針對來自 Azure 入口網站的 Azure 儲存體加密設定客戶管理的金鑰

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

本文說明如何使用[Azure 入口網站](https://portal.azure.com/)，以客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../../key-vault/quick-create-portal.md)。 

> [!IMPORTANT]
> 若要使用客戶管理的金鑰搭配 Azure 儲存體加密，需要在金鑰保存庫上設定兩個屬性： [虛**刪除**] 和 [**不要清除**]。 預設不會啟用這些屬性。 若要啟用這些屬性，請使用 PowerShell 或 Azure CLI。
> 僅支援 RSA 金鑰和金鑰大小2048。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰

若要在 Azure 入口網站中啟用客戶管理的金鑰，請遵循下列步驟：

1. 瀏覽至儲存體帳戶。
1. 在儲存體帳戶的 [設定] 刀鋒視窗上，按一下 [加密]。 選取 [使用您自己的金鑰] 選項，如下圖所示。

    ![顯示加密選項的入口網站螢幕擷取畫面](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>指定金鑰

啟用客戶管理的金鑰後，您將有機會指定與儲存體帳戶相關聯的金鑰。

### <a name="specify-a-key-as-a-uri"></a>以 URI 形式指定金鑰

若要指定金鑰做為 URI，請遵循下列步驟：

1. 若要找出 Azure 入口網站中的金鑰 URI，請流覽至您的金鑰保存庫，然後選取 [**金鑰**] 設定。 選取所需的金鑰，然後按一下金鑰以查看其設定。 複製提供 URI 的 [**金鑰識別碼**] 欄位的值。

    ![顯示金鑰保存庫金鑰 URI 的螢幕擷取畫面](media/storage-encryption-keys-portal/key-uri-portal.png)

1. 在儲存體帳戶的 [**加密**設定] 中，選擇 [**輸入金鑰 URI** ] 選項。
1. 在 [金鑰 URI] 欄位中，指定 URI。

   ![顯示如何輸入金鑰 URI 的螢幕擷取畫面](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>從金鑰保存庫指定金鑰

若要從金鑰保存庫指定金鑰，請先確定您有包含金鑰的金鑰保存庫。 若要從金鑰保存庫指定金鑰，請遵循下列步驟：

1. 選擇 [從 Key Vault 選取] 選項。
2. 選擇包含您想要使用之金鑰的金鑰保存庫。
3. 從金鑰保存庫選擇金鑰。

   ![顯示客戶管理的金鑰選項的螢幕擷取畫面](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須更新儲存體帳戶，才能使用新版本。 請遵循下列步驟：

1. 流覽至您的儲存體帳戶，並顯示**加密**設定。
1. 指定新金鑰版本的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](storage-service-encryption.md)
- [什麼是 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)？
