---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 825a9f5668cc80f1306d74623db2ea54614ba4a3
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985395"
---
> [!TIP]
> Azure 儲存體支援使用 Azure Active Directory (Azure AD) 來授權對 Blob 和佇列儲存體的要求。 使用 Azure AD 所傳回的 OAuth 2.0 權杖來授權使用者或應用程式, 可透過共用金鑰授權提供優異的安全性和易用性。 有了 Azure AD, 就不需要將帳戶存取金鑰與您的程式碼一起儲存, 並且可能會有潛在的安全性弱點。
>
> 此外, Azure 儲存體支援 Blob 儲存體的使用者委派共用存取簽章 (SAS)。 使用者委派 SAS 會使用 Azure AD 認證來簽署。 當您的應用程式設計需要共用存取簽章以存取 Blob 儲存體時, 請使用 Azure AD 認證來建立使用者委派 SAS, 以獲得更高的安全性。
>
> Microsoft 建議您盡可能使用 Azure AD 與您的 Azure 儲存體應用程式。 如需詳細資訊, 請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](../articles/storage/common/storage-auth-aad.md)。
