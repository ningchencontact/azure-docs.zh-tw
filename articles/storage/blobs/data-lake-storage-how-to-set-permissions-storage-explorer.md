---
title: 使用 Azure 儲存體總管設定 Data Lake Storage Gen2 的權限
description: 您可以透過這個方式，了解如何在您支援 Azure Data Lake Storage Gen2 (預覽) 的儲存體帳戶內部，使用 Azure 儲存體總管設定檔案和目錄的使用權限。
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: fd4ca3946ed4c32a8fd2f08c1c242c33dbca2aaf
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238308"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2-preview"></a>搭配 Azure Data Lake Storage Gen2 (預覽) 使用 Azure 儲存體總管設定檔案和目錄等級使用權限

Azure Data Lake Storage Gen2 (預覽) 中儲存的檔案支援微調權限和存取控制清單 (ACL) 管理。 微調權限和 ACL 管理的組合可讓您管理非常細部層級的資料存取。

您可在本文中了解如何使用 Azure 儲存體總管進行以下作業：

> [!div class="checklist"]
> * 設定檔案層級權限
> * 設定目錄層級權限
> * 將使用者或群組新增至存取控制清單

## <a name="prerequisites"></a>必要條件

為了能夠最貼切地描述此程序，我們需要您完成我們的 [Azure 儲存體總管快速入門](data-lake-storage-Explorer.md)。 這可以確保您的儲存體帳戶處於最適當的狀態 (已建立檔案系統且資料已經上傳到系統中)。

## <a name="managing-access"></a>管理存取

您可以在檔案系統根目錄設定權限。 若要這樣做，請以滑鼠右鍵按一下檔案系統，然後選取 [管理權限]，就會帶出 [管理權限] 對話方塊。

![Microsoft Azure 儲存體總管 - 管理目錄存取](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

[管理權限] 對話方塊可讓您管理擁有者和擁有者群組的權限。 它也可以讓您將新的使用者和群組新增到存取控制清單，方便之後管理他們的權限。

若要將新的使用者或群組加到存取控制清單，請選取 [新增使用者或群組] 欄位。

輸入和您想要新增至清單的相對應 Azure Active Directory (AAD) 項目，然後選取 [新增]。

使用者或群組現在將會出現在 [使用者和群組:] 欄位中，讓您可以開始管理他們的權限。

> [!NOTE]
> 最佳做法和建議是在 AAD 中建立安全性群組，並維護群組的權限，而不是維護個別使用者的權限。 如需這項建議和其他最佳做法的詳細資料，請參閱 [Data Lake Storage Gen2 的最佳做法](data-lake-storage-best-practices.md)。

您可以指派兩種權限：存取 ACL 和預設 ACL。

* **存取**：存取 ACL 可控制對物件的存取。 檔案和目錄均有存取 ACL。

* **預設**：與目錄相關聯 ACL 的範本，用以判斷在該目錄下所建立任何子項目的存取 ACL。 檔案沒有預設 ACL。

然後在這兩種類別內，您可以指派檔案或目錄的三種使用權限：[讀取]、[寫入]，以及 [執行]。

>[!NOTE]
> 在這裡選取的項目並不會在目錄內部目前現有的任何項目上設定使用權限。 如果檔案已經存在，必須移至每個項目手動設定使用權限。

您可以管理個別目錄及個別檔案的使用權限，也就是允許您進行更細部的存取控制。 管理目錄和檔案使用權限的程序與上面的描述相同。 以滑鼠右鍵按一下您想要管理使用權限的檔案或目錄，然後依照相同程序進行作業。

## <a name="next-steps"></a>後續步驟

您可以在這個操作說明中，了解如何使用 **Azure 儲存體總管**設定檔案和目錄的使用權限。 若要深入了解 ACL (包括預設 ACL、存取 ACL、它們的行為，以及它們的相對應權限)，請繼續閱讀和該主旨有關的概念性文章。

> [!div class="nextstepaction"]
> [Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)
