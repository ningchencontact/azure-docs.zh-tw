---
title: 如何設定 Azure Active Directory 原則，Azure 資料目錄
description: 您可以登入 Azure 資料目錄入口網站中，但當您嘗試登入資料來源註冊工具時，您可能會遇到的情況下，您會遇到的錯誤訊息。
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: 558f8845f5469bf157188e20f1ec65a07ff8355f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362970"
---
# <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 原則組態

您可能會遇到一種情況，您可以登入 Azure 資料目錄入口網站，但在您嘗試登入資料來源註冊工具時，您會遇到錯誤訊息，導致您無法登入。 當您在公司網路，或要從公司網路外部連接時，可能會發生這個錯誤。

## <a name="registration-tool"></a>註冊工具

註冊工具會使用「表單驗證」  ，根據 Azure Active Directory 驗證使用者登入。 Azure Active Directory 系統管理員必須在「全域驗證原則」 中啟用表單驗證，才會成功登入。

利用全域驗證原則，您可以分別為內部網路和外部網路連線啟用驗證方法，如下圖所示。 如果您要從中連線的網路未啟用表單驗證，可能會發生登入錯誤。

 ![Azure Active Directory 全域驗證原則](./media/troubleshoot-policy-configuration/global-auth-policy.png)

如需詳細資訊，請參閱 [設定驗證原則](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 資料目錄](data-catalog-get-started.md)