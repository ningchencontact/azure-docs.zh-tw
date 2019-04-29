---
title: Threat Modeling Tool 版本 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: 記載 Threat Modeling Tool 的版本資訊
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610625"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>威脅模型化工具更新版本 7.1.60408.1-4/9/2019

版本 7.1.60408.1 的 Microsoft 威脅模型化工具 (TMT) 已於 4 月 9 2019年發行，並包含下列變更：

- Azure 金鑰保存庫和 Azure 流量管理員的新樣板
- TMT 版本號碼現在會顯示在主畫面上
- 已更新的支援連結
- 錯誤修正

## <a name="feature-changes"></a>功能變更

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Azure 金鑰保存庫和 Azure 流量管理員的新樣板

![Azure 金鑰保存庫樣板](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

新的樣板和 Azure 金鑰保存庫和 Azure 流量管理員的威脅已新增至 Azure 的樣板集。 開啟時在 Azure 的樣板集為基礎的模型，將會提示使用者更新與模型相關聯的範本。 更新模型，根據 Azure 的樣板集也可手動起始的 [檔案] 功能表中使用 「 套用範本 」 命令，並重新套用最新的 Azure 雲端 Services.tb7 檔案。

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>TMT 版本號碼現在會顯示在主畫面上

為了方便存取的應用程式的主畫面上，現在會顯示威脅模型化工具的用戶端版本。

![Azure 金鑰保存庫樣板](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>已更新的支援連結

已更新在此工具中的所有支援連結，以將使用者導向[ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com)而不是 MSDN 論壇。

## <a name="system-requirements"></a>系統需求

- 支援的作業系統
  - [Microsoft Windows 10 年度更新版](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更新版本
- 必要的 .NET 版本
  - [.Net 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) 或更新版本
- 其他需求
  - 必須有網際網路連線可接收工具和範本的更新。

## <a name="documentation-and-feedback"></a>文件和意見反應

- Threat Modeling Tool 的文件位於 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)，其中包含[關於使用工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)的資訊。

## <a name="next-steps"></a>後續步驟

下載最新版的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。