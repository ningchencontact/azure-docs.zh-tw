---
title: Azure 資訊安全中心的 Azure 儲存體帳戶的加密 |Microsoft Docs
description: 本文件說明如何實作 Azure 資訊安全中心建議：**啟用 Azure 儲存體帳戶的加密**。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: cadf375b541900600be82e76673637ff01a8ad96
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696078"
---
# <a name="encryption-for-azure-storage-account-in-azure-security-center"></a>Azure 資訊安全中心的 Azure 儲存體帳戶的加密

所有 Azure 儲存體帳戶的預設會啟用待用資料的 azure 儲存體加密。 寫入 Azure 儲存體，會先擷取解密時，azure 儲存體加密的資料。 如需詳細資訊，請參閱 <<c0> [ 待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。

如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) - 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) - 了解如何監視 Azure 資源的健全狀況。
* [在 Azure 資訊安全中心管理與回應安全性警示](security-center-managing-and-responding-alerts.md) - 了解如何管理與回應安全性警示。
* [在 Azure 資訊安全中心管理安全性建議](security-center-recommendations.md) - 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) - 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/) - 尋找有關 Azure 安全性與合規性的部落格文章。

<!--Image references-->
[1]: ./media/security-center-enable-encryption-for-storage-account/enable-encryption-for-storage-account.png
[2]: ./media/security-center-enable-encryption-for-storage-account/enable-storage-encryption.png
[3]: ./media/security-center-enable-encryption-for-storage-account/encryption-blade.png
