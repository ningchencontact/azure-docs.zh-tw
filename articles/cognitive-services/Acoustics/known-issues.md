---
title: Project Acoustics 外掛程式的已知問題
titlesuffix: Azure Cognitive Services
description: 使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylsto
ROBOTS: NOINDEX
ms.openlocfilehash: df5a4ce62af0405ffab8f711fb40b3d92083a3a4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706653"
---
# <a name="project-acoustics-known-issues"></a>聲場專案的已知問題
使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>將場景重新命名時遺失聲場參數

如果您重新命名場景, 所有屬於該場景的聲場參數都不會自動傳送至新場景。 不過, 它們仍會存在於舊的資產檔案中。 請在 [Editor] 目錄內您場景檔案的旁邊尋找 **SceneName_AcousticParameters.asset** 檔案。 請將您的檔案重新命名以反映新的場景名稱。

## <a name="unity-crashes-when-closing-project"></a>關閉專案時 Unity 當機

在最新版 Unity (2018.2+) 上，有一個 Unity 在您關閉專案時會當機的已知錯誤 (Bug)。 [這個 Unity 問題](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)已追蹤此錯誤。

## <a name="deploying-to-android-from-some-unity-versions"></a>從某些 Unity 版本部署至 Android

某些版本的 Unity 有 bug, 將音訊外掛程式部署至 Android。 請確定您不是使用受[此錯誤](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)影響的版本。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>我收到 'could not find metadata file System.Security.dll' (找不到中繼資料檔案 System.Security.dll) 的錯誤

請確定播放器設定中的 [Scripting Runtime Version] \(指令碼處理執行階段版本\) 已設定為 [.NET 4.x Equivalent] \(.NET 4.x 對等\)，然後重新啟動 Unity。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>我在連線至 Azure 時發生驗證問題

請仔細檢查您用於 Auzre 帳戶的認證是否正確、您的帳戶是否支援製作項目中所要求的節點類型，以及您的系統時鐘是否正確。

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>取消製作會使 [製作] 索引標籤處於「正在刪除」狀態
聲場專案會在成功完成或取消時, 清除作業的所有 Azure 資源。 這可能需要5分鐘的時間。

## <a name="next-steps"></a>後續步驟
* 嘗試[Unity](unity-quickstart.md)或[Unreal](unreal-quickstart.md)範例內容

