---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161289"
---
下列注意事項適用於正移至 Azure 的資料。

- 我們建議不要將多個裝置寫入至相同的容器。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，裝置將會覆寫雲端中的檔案。
- 在共用資料夾下建立的空目錄階層 (不含任何檔案) 則不會上傳至 Blob 容器。
- 您可以使用拖放功能將資料複製，並卸除與檔案總管 中，或透過命令列。 如果要複製的檔案的彙總大小大於 10 GB，建議您在使用如 Robocopy 或 rsync 的大量複製程式。 大量複製工具重試複製作業的間歇性的錯誤，並提供額外的復原。
- 如果與 Azure 儲存體容器相關的共用上傳不符合於建立時針對共用所定義之 Blob 類型的 Blob，則這類 Blob 將不會被更新。 例如，您在裝置上建立區塊 Blob 共用。 將該共用與具有分頁 Blob 的現有雲端容器相關聯。 重新整理該共用以下載檔案。 修改部分已重新整理，且已經在雲端中儲存為分頁 Blob 的檔案。 您將會看見上傳失敗。
