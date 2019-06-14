---
title: Key Vault 版本
description: 各種 Azure Key Vault 版本
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: e452313934c6a3076a3801b70019048090f2c6d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64685448"
---
# <a name="key-vault-versions"></a>Key Vault 版本

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - 受控儲存體帳戶金鑰

2017 夏天 - 儲存體帳戶金鑰功能已新增可與 Azure 儲存體更輕鬆地整合。 如需詳細資訊，請參閱概觀主題：[受控儲存體帳戶金鑰概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)。

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - 虛刪除

2017 夏天 - 已新增虛刪除功能，改善金鑰保存庫和金鑰保存庫物件的資料保護。 如需詳細資訊，請參閱概觀主題：[虛刪除概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)。

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - 憑證管理

在 2016 年 9 月 26 日，憑證管理已作為功能新增至 GA 版本 2015-06-01。

## <a name="2015-06-01---general-availability"></a>2015-06-01 - 正式運作

正式運作版本 2015-06-01，於 2015 年 6 月 24 日宣告。

此版本已進行下列變更：

- 刪除金鑰 - 已移除 [使用] 欄位。
- 取得金鑰的相關資訊 - 已移除 [使用] 欄位。
- 將金鑰匯入保存庫 - 已移除 [使用] 欄位。
- 還原金鑰 - 已移除 [使用] 欄位。
- 針對 RSA 演算法，已將 "RSA_OAEP" 變更為 "RSA-OAEP"。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-and-certificates.md)。

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

第二個預覽版本 2015-02-01-preview，於 2015 年 4 月 20 日宣告。 如需詳細資訊，請參閱 [REST API 更新](https://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx)部落格文章。

已更新下列工作：

- 列出保存庫中的工作 - 已將分頁支援新增至作業。
- 列出金鑰的版本 - 已新增作業，來列出金鑰的版本。
- 列出保存庫中的祕密 - 已新增分頁支援。
- 列出祕密的版本 - 新增作業，來列出祕密的版本。
- 所有作業 - 已將建立/更新時間戳記新增至屬性。
- 建立祕密 - 已將 Content-Type 新增至祕密。
- 建立金鑰 - 已將標籤新增為選用資訊。
- 建立祕密 - 已將標籤新增為選用資訊。
- 更新金鑰 - 已將標籤新增為選用資訊。
- 更新祕密 - 已將標籤新增為選用資訊。
- 已將祕密的大小上限從 10 K 變更為 25 K 位元組。 請參閱[關於金鑰、祕密與憑證](about-keys-secrets-and-certificates.md)。

## <a name="2014-12-08-preview"></a>2014-12-08-preview

第一個預覽版本 2014-12-08-preview，於 2015 年 1 月 8 日宣告。

## <a name="see-also"></a>請參閱
- [關於金鑰、密碼與憑證](about-keys-secrets-and-certificates.md)
