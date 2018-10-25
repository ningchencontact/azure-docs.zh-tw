---
title: 新增適用於 Azure Stack ADFS 的使用者 | Microsoft Docs
description: 了解如何新增適用於 Azure Stack 之 ADFS 部署的使用者
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344161"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>在 AD FS 中新增 Azure Stack 使用者
您可以使用 [Active Directory 使用者和電腦] 嵌入式管理單元，將其他使用者新增到運用 AD FS 做為其身分識別提供者的 Azure Stack 環境。

## <a name="add-windows-server-active-directory-users"></a>新增 Windows Server Active Directory 使用者
> [!TIP]
> 此範例使用預設的 azurestack.local ASDK Active Directory。 

1.  使用可存取 Windows 系統管理工具和開啟全新 Microsoft Management Console (MMC) 的帳戶登入電腦。
2.  按一下 [檔案] > [新增或移除嵌入式管理單元]。
3.  選取 [Active Directory 使用者和電腦] > [AzureStack.local] > [使用者]。
4.  按一下 [動作] > [新增] > [使用者]。
5.  在 [新增物件 - 使用者] 視窗中，提供並確認密碼
6.  按一下 [下一步] 以完成值，然後按一下 [完成] 來建立使用者。


## <a name="next-steps"></a>後續步驟
[建立服務主體](azure-stack-create-service-principals.md)