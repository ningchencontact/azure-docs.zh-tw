---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333814"
---
下列注意事項適用於正移至 Azure 的資料。

- 我們建議不要將多個裝置寫入至相同的容器。
- 如果雲端中現有的 Azure 物件 (例如 Blob 或檔案) 與要複製的物件同名，裝置將會覆寫雲端中的檔案。
- 在共用資料夾下建立的空目錄階層 (不含任何檔案) 則不會上傳至 Blob 容器。
- 對於大型檔案，建議您使用 robocopy，因為這會在間歇性錯誤發生時重試複製作業。
- 如果與 Azure 儲存體容器相關的共用上傳不符合於建立時針對共用所定義之 Blob 類型的 Blob，則這類 Blob 將不會被更新。 例如，您在裝置上建立區塊 Blob 共用。 將該共用與具有分頁 Blob 的現有雲端容器相關聯。 重新整理該共用以下載檔案。 修改部分已重新整理，且已經在雲端中儲存為分頁 Blob 的檔案。 您將會看見上傳失敗。
