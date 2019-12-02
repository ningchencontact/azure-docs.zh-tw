---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665896"
---
Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以在儲存體帳戶層級提供客戶管理的金鑰。

客戶管理的金鑰必須儲存在 Azure Key Vault 中。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。 如需 Azure 儲存體加密和金鑰管理的詳細資訊，請參閱[靜態資料的 Azure 儲存體加密](../articles/storage/common/storage-service-encryption.md)。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../articles/key-vault/key-vault-overview.md)
