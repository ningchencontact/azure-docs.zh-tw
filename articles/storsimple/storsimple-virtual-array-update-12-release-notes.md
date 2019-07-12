---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 版本資訊 |Microsoft Docs
description: 描述執行 Update 1.2 的 StorSimple Virtual Array 的重大未決問題和解決方式。
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420599"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 版本資訊

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

會持續更新的版本資訊。 當發現需要因應措施的重大問題時，它們會將它加入。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

對應於軟體版本的 update 1.2 **10.0.10311.0**。

> [!IMPORTANT]
> - 更新是干擾性作業，會重新啟動您的裝置。 如果 I/O 正在進行中，裝置就會停機。 如需用來套用此更新套件的詳細指示，請移至[下載 Update 1.2](#download-update-12)。
> - 只有當您的裝置執行 Update 1.0 或 1.1 時，才可供您透過 Azure 入口網站 update 1.2。

## <a name="whats-new-in-update-12"></a>Update 1.2 的新功能

此更新包含下列的錯誤修正：

- 改善恢復功能，處理時刪除檔案。
- 改進的處理例外狀況，導致的程式碼啟動路徑中減少備份、 還原、 雲端讀取失敗，並自動化空間回收。

## <a name="download-update-12"></a>下載 Update 1.2

若要下載此更新，請前往[Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx)伺服器，以及下載 KB4502035 封裝。 此套件包含下列封裝：

 - **KB4493446** 2019 年 4 月至 2012 R2 包含累加的 Windows 更新。 如需有關此彙總中包含哪些內容的詳細資訊，請移至[年 4 月每月安全性彙總](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)。
 - **KB3011067**這是 Microsoft Update 獨立封裝檔案 WindowsTH-KB3011067-x64。 此檔案用來更新裝置軟體。

下載 KB4502035 並遵循這些指示[透過本機 web UI 將更新套用](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="issues-fixed-in-update-12"></a>在 Update 1.2 中修正的問題

下表提供本版已修正問題的摘要。

| 資料分割 | 功能 | 問題 |
| --- | --- | --- |
| 1 |刪除| 在舊版的軟體中，發生問題時使用裝置未變更，即使檔案已刪除。 在這個版本會修正此問題。 處理刪除的檔案時進行階層處理的程式碼路徑更有彈性。|
| 2 |例外狀況處理| 在舊版的軟體中，發生下列系統重新開機，可能會導致備份，還原讀取雲端中的失敗，並自動化空間回收問題。 此版本包含有關的啟動路徑中所處理的例外狀況的變更。|

## <a name="known-issues-in-update-12"></a>Update 1.2 中的已知問題

沒有新的問題是版本所述，在 Update 1.2。 從之前的版本會保留所有所列的問題。 若要查看來自舊版本所包含的已知問題的摘要，請前往[Update 1.1 中的已知問題](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11)。

## <a name="next-steps"></a>後續步驟

下載 KB4502035 並[透過本機 web UI 將更新套用](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)。

## <a name="references"></a>參考

要尋找舊版本資訊嗎？ 請移至：
* [StorSimple Virtual Array Update 1.1 版本資訊](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 版本資訊](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 版本資訊](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 版本資訊](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 版本資訊](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 版本資訊](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式運作版版本資訊](storsimple-ova-pp-release-notes.md)
