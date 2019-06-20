---
title: 如何疑難排解 Azure 資料目錄
description: 本文說明 Azure 資料目錄資源的常見疑難排解問題。
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203495"
---
# <a name="troubleshooting-azure-data-catalog"></a>疑難排解 Azure 資料目錄

本文說明 Azure 資料目錄資源的常見疑難排解問題。 

## <a name="functionality-limitations"></a>功能限制

使用 Azure 資料目錄，下列功能時，限制：

- 帳戶類型**來賓角色**不支援。 您無法將來賓帳戶新增為 Azure 資料目錄的使用者和來賓使用者無法在 www.azuredatacatalog.com 使用入口網站。

- 不支援建立使用 Azure Resource Manager 範本或 Azure PowerShell 命令的 Azure 資料目錄資源。

- Azure 租用戶之間無法移動的 Azure 資料目錄資源。

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory 原則組態

您可能會遇到一種情況，您可以登入 Azure 資料目錄入口網站，但在您嘗試登入資料來源註冊工具時，您會遇到錯誤訊息，導致您無法登入。 當您在公司網路，或要從公司網路外部連接時，可能會發生這個錯誤。

註冊工具會使用「表單驗證」  ，根據 Azure Active Directory 驗證使用者登入。 Azure Active Directory 系統管理員必須在「全域驗證原則」  中啟用表單驗證，才會成功登入。

利用全域驗證原則，您可以分別為內部網路和外部網路連線啟用驗證方法，如下圖所示。 如果您要從中連線的網路未啟用表單驗證，可能會發生登入錯誤。

 ![Azure Active Directory 全域驗證原則](./media/troubleshoot-policy-configuration/global-auth-policy.png)

如需詳細資訊，請參閱 [設定驗證原則](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11))。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 資料目錄](data-catalog-get-started.md)
