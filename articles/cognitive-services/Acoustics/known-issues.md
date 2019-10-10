---
title: 聲場專案外掛程式的已知問題
titlesuffix: Azure Cognitive Services
description: 在聲場專案中，您可能會遇到下列已知問題。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243030"
---
# <a name="project-acoustics-known-issues"></a>聲場專案的已知問題
本文說明當您使用聲場專案時可能會遇到的問題。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>將場景重新命名時遺失聲場參數

如果您重新命名場景，所有屬於該場景的聲場參數都不會自動傳送至新場景。 但它們仍然存在於舊的資產檔案中。 在您場景檔案旁的*編輯器*目錄中，尋找 *[SceneName] _AcousticParameters 資產*檔案。 將檔案重新命名，以反映新的場景名稱。

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>從部分 Unity 版本部署至 Android 的 bug

某些版本的 Unity 在將音訊外掛程式部署至 Android 方面有[bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) 。 請確定您不是使用受此錯誤影響的版本。

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>「找不到元資料檔案系統。安全性 .dll」錯誤

請確定**Player**設定中的**腳本執行階段版本**是 .net 4.x 對*等*的，然後重新開機 Unity。

## <a name="authentication-problems-when-connecting-to-azure"></a>連接到 Azure 時的驗證問題

檢查：
- 您已使用正確的 Azure 帳號憑證。
- 您的帳戶支援您在製作中要求的節點類型。
- 已正確設定您的系統時鐘。

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>取消之後，[製作] 索引標籤仍會顯示 [刪除]
聲場專案在成功完成或取消之後，清除作業的所有 Azure 資源。 此程式最多可能需要5分鐘的時間。

## <a name="next-steps"></a>後續步驟
* 嘗試[Unity](unity-quickstart.md)或[Unreal](unreal-quickstart.md)範例內容。
