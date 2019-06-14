---
title: 如何從 Azure DevTest Labs 刪除及匯出個人資料 | Microsoft Docs
description: 了解如何從 Azure DevLast Labs 服務刪除及匯出個人資料，以履行您在一般資料保護規定 (GDPR) 下的責任。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394903"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>從 Azure DevTest Labs 匯出或刪除個人資料
本文提供從 Azure DevTest Labs 服務刪除及匯出個人資料的步驟。 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs 會收集哪些個人資料？
DevTest Labs 會對使用者收集兩種主要的個人資料。 這兩種資料是使用者電子郵件地址和使用者物件識別碼。 這些都是服務為實驗室管理員和實驗室使用者提供服務功能所不可或缺的資訊。

### <a name="user-email-address"></a>使用者電子郵件地址
DevTest Labs 會依據使用者電子郵件地址，將自動關閉電子郵件通知傳送給實驗室使用者。 此電子郵件會通知使用者其機器即將關閉。 使用者可依需求自行選擇延後或略過關機作業。 您可以設定實驗室層級或 VM 層級的電子郵件地址。

**設定實驗室的電子郵件：**

![設定實驗室層級的電子郵件](./media/personal-data-delete-export/lab-user-email.png)

**設定 VM 的電子郵件：**

![設定 VM 層級的電子郵件](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>使用者物件識別碼
DevTest Labs 會透過使用者物件識別碼向實驗室管理員顯示逐月的成本趨勢，以及依資源列出的成本資訊。 它可讓管理員追蹤成本及管理其實驗室的閾值。 

**目前行事曆月份的預估成本趨勢：** 
![目前行事曆月份的預估成本趨勢](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**依資源估計的當月成本：** 
![依資源估計的當月成本](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>我們為何需要這項個人資料？
DevTest Labs 服務會基於運作目的使用個人資料。 這是服務為了提供重要功能所不可或缺的資料。 如果您設定了使用者電子郵件地址的保留原則，實驗室使用者在其電子郵件地址從我們的系統中刪除後，就無法適時收到自動關閉電子郵件通知。 同樣地，如果使用者物件識別碼依據保留原則而刪除，實驗室管理員也無法就其在實驗室中的機器檢視逐月成本趨勢和個別資源的成本。 因此，只要使用者的資源在實驗室中仍有效用，就必須保留這項資料。

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>如何讓系統消除我的個人資料？
如果您是實驗室使用者，當您想要刪除這項個人資料時，可以藉由刪除實驗室中的對應資源來達到此目的。 DevTest Labs 服務會在使用者刪除其個人資料的 30 天後，將已刪除的資料匿名處理。

例如，如果您刪除了 VM，或移除了電子郵件地址，DevTest Labs 服務將會在資源刪除的 30 天後將這項資料匿名處理。 刪除後保留 30 天的原則，是為了確保我們能為實驗室管理員提供精確的逐月成本預測。

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>如何要求匯出我的個人資料？
身為實驗室使用者，您可以要求匯出 DevTest Labs 服務所儲存的個人資料。 若要要求匯出，請瀏覽至實驗室 [概觀]  頁面上的 [個人資料]  選項。 選取 [要求匯出]  按鈕，即可開始在您實驗室管理員的儲存體帳戶中建立可下載的 Excel 檔案。 接著，您可以連絡實驗室管理員以檢視這項資料。

1. 選取左側功能表上的 [個人資料]  。 

    ![個人資料頁面](./media/personal-data-delete-export/personal-data-page.png)
2. 選取包含實驗室的**資源群組**。

    ![選取資源群組](./media/personal-data-delete-export/select-resource-group.png)
3. 選取資源群組中的**儲存體帳戶**。
4. 在 [儲存體帳戶]  頁面上，選取 [Blob]  。

    ![選取 Blob 圖格](./media/personal-data-delete-export/select-blobs-tile.png)
5. 在容器清單中選取名為 **labresourceusage** 的容器。

    ![選取 Blob 容器](./media/personal-data-delete-export/select-blob-container.png)
6. 選取以您的實驗室命名的**資料夾**。 在此資料夾中，您會看到您實驗室中的**磁碟**和**虛擬機器**的 **csv** 檔案。 您可以下載這些 csv 檔案、篩選出要求存取的實驗室使用者所適用的內容，並與他們共用。

    ![下載 CSV 檔案](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>後續步驟
請參閱下列文章： 

- [設定實驗室的原則](devtest-lab-get-started-with-lab-policies.md)
- [常見問題集](devtest-lab-faq.md)
