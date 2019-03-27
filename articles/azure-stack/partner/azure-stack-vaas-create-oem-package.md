---
title: Azure Stack 驗證最佳做法 | Microsoft Docs
description: 本文描述使用「驗證即服務」的最佳做法。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 568817c6e25952f15a396e5748d314187345945b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769041"
---
# <a name="create-an-oem-package"></a>建立 OEM 套件

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

「Azure Stack OEM 延伸模組套件」是一個機制，藉由此機制即可將 OEM 特定內容新增至 Azure Stack 基礎結構，以供在部署及操作程序 (例如更新、擴充及欄位取代) 中使用。

## <a name="creating-the-package"></a>建立套件

OEM 延伸模組套件在建立並通過驗證之後，便可在 VaaS 中使用。  在繼續進行之前，請確定您已完成[建立 OEM 套件](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true)的步驟。 接著會將此套件連同 VaaS 測試結果一起提交給 Microsoft，以供在「套件驗證」工作流程中簽署。 下列步驟會詳細說明如何將所產生的檔案組成 VaaS 能夠取用的單一 ZIP 檔案。

1. 識別下列用於套件的內容：
    - 包含套件內容的 ZIP 檔案
    - 一個名為 `oemMetadata.xml` 資訊清單檔，此檔案的內容應該與套件內容根目錄中的 metadata.xml 檔案相同。

2. 選取內容檔案，然後從內容建立 ZIP 檔案：

    ![ZIP 檔案內容](media/vaas-create-oem-package-1.png) ![壓縮項目內容](media/vaas-create-oem-package-2.png)

3. 將產生的檔案重新命名來提供足夠的描述，以便您識別檔案。

## <a name="verifying-the-contents"></a>確認內容

若要驗證您 ZIP 檔案的結構，請檢查它並確認其中沒有任何子資料夾。 TLD 包含已壓縮的內容。 以下顯示有效的套件結構。
> [!IMPORTANT]
> 壓縮父資料夾而不是內容將會導致套件簽署失敗。

![以正確方式壓縮的套件內容](media/vaas-create-oem-package-3.png)

ZIP 檔案現在已可上傳到 VaaS，並由 Microsoft 在「套件驗證」工作流程中簽署。

## <a name="next-steps"></a>後續步驟

- [驗證 OEM 套件](azure-stack-vaas-validate-oem-package.md)
