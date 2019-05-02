---
title: 最佳做法要領開始使用 Azure Key Vault 的 Key Vault-|Microsoft Docs
description: 本文件說明一些使用金鑰保存庫的最佳作法
services: key-vault
author: mbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 27881758175ab698e4b713c0215383878272382f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700757"
---
# <a name="best-practices-to-use-key-vault"></a>若要使用金鑰保存庫的最佳作法

## <a name="control-access-to-your-vault"></a>控制您的保存庫的存取

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 由於這項資料相當敏感且攸關業務，所以您必須藉由只允許獲得授權的應用程式和使用者，來保護金鑰保存庫的存取權。 這[文章](key-vault-secure-your-key-vault.md)提供的 Key Vault 存取模型的概觀。 文中會說明驗證和授權，並說明如何保護金鑰保存庫的存取權。

控制對您的保存庫的存取時的建議如下所示：
1. 鎖住您的訂用帳戶、 資源群組和金鑰保存庫 」 (RBAC) 的存取
2. 建立每個保存庫的存取原則
3. 使用最低權限存取主體授與存取
4. 開啟防火牆和[VNET 服務端點](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>使用個別的金鑰保存庫

我們的建議是使用每個應用程式，每個環境 （開發、 進入生產階段前和生產） 的保存庫。 這可協助您的環境之間共用祕密，而也能降低發生漏洞時的威脅。

## <a name="backup"></a>Backup 

請確認您採取定期回復的 ups 您[保存庫](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/)update/delete/建立保存庫中的物件。

## <a name="turn-on-logging"></a>開啟記錄功能

[開啟記錄](key-vault-logging.md)保存庫。 也請設定警示。

## <a name="turn-on-recovery-options"></a>開啟復原選項

1. 開啟[虛刪除](key-vault-ovw-soft-delete.md)。
2. 如果您想要防範的祕密的強制刪除 / 保存庫虛刪除開啟時，即使，開啟清除保護。
