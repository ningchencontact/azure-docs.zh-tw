---
title: azcopy |Microsoft Docs
description: 本文提供 azcopy 命令的參考資訊。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0772446b0259b12d3f76b2020eeed4c9c2de1119
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882211"
---
# <a name="azcopy"></a>azcopy

AzCopy 是一種命令列工具，可將資料移入和移出 Azure 儲存體。

## <a name="synopsis"></a>摘要

命令的一般格式為： `azcopy [command] [arguments] --[flag-name]=[flag-value]`。

若要報告問題或深入瞭解此工具，請參閱[https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)。

## <a name="options"></a>選項

**--cap-mbps uint32**  上限（以每秒 mb 為單位）傳輸速率。 時間點的輸送量可能會與端點略有不同。 如果此選項設定為零或省略，則輸送量不會限制。

**-h、--help**Azcopy 的說明
      
**--output-類型** 命令輸出的格式。 選項包括： text、json。 預設值為「文字」。 （預設值為 "text"）

## <a name="see-also"></a>請參閱

- [開始使用 AzCopy](storage-use-azcopy-v10.md)
- [azcopy 工作臺](storage-ref-azcopy-bench.md)
- [azcopy 複製](storage-ref-azcopy-copy.md)
- [azcopy 檔](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy 作業](storage-ref-azcopy-jobs.md)
- [清除 azcopy 作業](storage-ref-azcopy-jobs-clean.md)
- [azcopy 作業清單](storage-ref-azcopy-jobs-list.md)
- [azcopy 作業移除](storage-ref-azcopy-jobs-remove.md)
- [azcopy 作業繼續](storage-ref-azcopy-jobs-resume.md)
- [azcopy 作業顯示](storage-ref-azcopy-jobs-show.md)
- [azcopy 清單](storage-ref-azcopy-list.md)
- [azcopy 登入](storage-ref-azcopy-login.md)
- [azcopy 登出](storage-ref-azcopy-logout.md)
- [azcopy make](storage-ref-azcopy-make.md)
- [azcopy 移除](storage-ref-azcopy-remove.md)
- [azcopy 同步](storage-ref-azcopy-sync.md)
