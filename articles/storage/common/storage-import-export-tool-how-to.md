---
title: 使用 Azure 匯入/匯出工具 | Microsoft Docs
description: 了解如何使用匯入/匯出工具來準備硬碟機，以進行匯入作業、修復匯入作業或修復匯出作業。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 5eaf29623a18f7347ad287e4b8389667f4b4e272
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467555"
---
# <a name="using-the-azure-importexport-tool"></a>使用 Azure 匯入匯出工具 

Azure 匯入/匯出工具 (WAImportExport.exe) 是用來建立及管理 Azure 匯入/匯出服務的作業，可讓您將大量資料傳入或傳出 Azure Blob 儲存體。

本文件適用於最新版本的 Azure 匯入/匯出工具。 如需使用傳統部署模型的相關資訊，請參閱[使用 Azure 匯入/匯出工具 v1](storage-import-export-tool-how-to-v1.md)。

下列文章會示範如何操作：  

- 安裝並設定 Azure 匯入/匯出工具。
- 準備硬碟機進行將資料從您的磁碟機匯入 Azure Blob 儲存體的作業。
- 使用複製記錄檔檢閱作業的狀態。 
- 修復匯入作業。 
- 修復匯出作業。 
- 針對 Azure 匯入/匯出工具進行疑難排解。 

## <a name="next-steps"></a>後續步驟

* [設定 WAImportExport 工具](storage-import-export-tool-setup.md)
