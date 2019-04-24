---
title: 發生影響 Azure Key Vault 的 Azure 服務中斷事件時該怎麼辦 - Azure Key Vault | Microsoft Docs
description: 了解發生影響「Azure 金鑰保存庫」的 Azure 服務中斷事件時該怎麼辦。
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
editor: ''
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: dd1d2af174f93cceabccd62a78d45a0e4fbed502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305436"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure 密钥保管库可用性和冗余

Azure 密钥保管库具有多层冗余功能，确保密钥和机密持续可供应用程序使用，即使服务的单个组件发生故障也是如此。

金鑰保存庫的內容會在區域內複寫，以及複寫到至少距離 150 英哩但位於相同地理位置內的次要區域。 這可維持您金鑰和密碼的高持久性。 請參閱 [Azure 配對的區域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)文件，以取得特定區域配對的詳細資料。

如果金鑰保存庫服務內的個別元件失敗，則區域內的替代元件會接替來處理您的要求，以確保不會導致功能的效能降低。 不需要采取任何措施即可触发此功能。 它會以您無法察覺的方式自動發生。

在整個 Azure 區域都無法供使用的罕見情況下，您在該區域中所發出的「Azure 金鑰保存庫」要求會自動路由傳送 (容錯移轉) 到次要地區。 當主要區域再次可用時，要求就會路由傳送回 (容錯回復) 主要區域。 同樣地，您不需要採取任何動作，因為這會自動發生。

有一些要注意的警告事項：

* 發生區域容錯移轉時，可能需要幾分鐘讓服務進行容錯移轉。 在這段時間直到容錯移轉完成之前所發出的要求可能會失敗。
* 容錯移轉完成之後，您的金鑰保存庫會處於唯讀模式。 在此模式下支持的请求包括：
  * 列出金鑰保存庫
  * 取得金鑰保存庫的屬性
  * 列出密碼
  * 取得密碼
  * 列出金鑰
  * 取得金鑰 (的屬性)
  * 加密
  * 解密
  * 包裝
  * 解除包裝
  * 驗證
  * 簽署
  * Backup 
* 在容錯移轉進行容錯回復之後，所有要求類型 (包括讀取「和」寫入要求) 都會可供使用。

