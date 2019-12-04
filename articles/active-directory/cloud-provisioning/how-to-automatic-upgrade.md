---
title: Azure AD Connect 雲端布建代理程式：自動升級 |Microsoft Docs
description: 本主題說明 Azure AD Connect 雲端布建代理程式中的內建自動升級功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794454"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 雲端布建代理程式：自動升級

使用**自動升級**功能，確保您的 Azure AD Connect 雲端布建代理程式安裝永遠保持最新狀態。 此功能預設為啟用，且無法停用。

代理程式安裝于此處：「 **Program FILES\AZURE AD Connect**布建 Agent\AADConnectProvisioningAgent.exe」

以滑鼠右鍵按一下可執行檔，然後依序選取 [屬性] 和 [詳細資料]，即可驗證您的版本。

![代理程式檔案版本](media/how-to-automatic-upgrade/agent1.png)

代理程式更新程式安裝于此處：「 **Program FILES\AZURE AD Connect 布建代理程式 Updater\AzureADConnectAgentUpdater.exe** 」

以滑鼠右鍵按一下可執行檔，然後依序選取 [屬性] 和 [詳細資料]，即可驗證您的版本。

![代理程式更新程式版本](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>卸載代理程式
若要移除代理程式，請流覽至 [**卸載或變更程式**] 並卸載下列專案：

- Microsoft Azure AD Connect 代理程式更新程式
- Microsoft Azure AD Connect 佈建代理程式
- Microsoft Azure AD Connect 佈建代理程式套件

![代理程式移除](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>後續步驟 

- [什麼是布建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端布建？](what-is-cloud-provisioning.md)

