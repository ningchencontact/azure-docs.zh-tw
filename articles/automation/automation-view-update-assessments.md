---
title: 查看 Azure 更新管理更新評量
description: 本文說明如何查看更新部署的更新評量
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377716"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新評量

在您的自動化帳戶中，選取 [更新管理] 來檢視機器的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 [合規性欄] 中，您可以看到機器上次的評估時間。 在 [更新代理程式整備程度] 欄中，您可以查看更新代理的健康情況。 如果發生問題，請選取連結來移至疑難排解文件，協助您了解應該執行哪些步驟來更正問題。

若要執行記錄搜尋來傳回與機器、更新或部署相關的資訊，請選取清單中的項目。 [記錄搜尋] 窗格隨即開啟，並顯示所選項目的查詢：

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 畫面上會顯示需要更新的機器數目、作業系統以及一個能提供詳細資訊的連結。 [記錄搜尋] 窗格會顯示與更新有關的詳細資訊。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分類

下表列出「更新管理」中的更新分類清單，以及每個分類的定義。

### <a name="windows"></a>Windows

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責處理與安全性無關的重大錯誤。        |
|安全性更新     | 特定產品的安全性相關更新。        |
|更新彙總套件     | 一組累計的 Hotfix，封裝在一起以便於部署。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的累計 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前安裝之應用程式或檔案的更新。        |

### <a name="linux-2"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 在性質上不重要或非安全性更新的所有其他更新。        |

針對 Linux，「更新管理」可以區分雲端中的重大更新和安全性更新，同時由於雲端中的資料擴充而顯示評估資料。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版本不同，CentOS 預設沒有此資訊。 如果您以某種方式將 CentOS 機器設定成傳回以下命令的安全性資料，則「更新管理」將能夠根據分類來進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可以在 CentOS 上啟用原生分類資料可用性。 目前，只為那些已自行啟用此功能的客戶，提供盡力而為的支援。

## <a name="next-steps"></a>後續步驟

在您查看任何更新評估之後，您可以遵循[管理 Azure vm 的更新和修補程式](automation-tutorial-update-management.md)底下的步驟來排程更新部署。