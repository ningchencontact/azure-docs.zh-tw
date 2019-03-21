---
title: Project Acoustics 外掛程式的已知問題
titlesuffix: Azure Cognitive Services
description: 使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 4cbede768409596365bea0cdbbb451cc5195ac4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136254"
---
# <a name="project-acoustics-known-issues"></a>專案樂器的已知問題
使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>將場景重新命名時遺失聲場參數

如果您重新命名為場景，所有屬於該場景的原音參數將不會自動轉移至新的場景。 它們將仍存在於舊的資產檔案不過。 請在 [Editor] 目錄內您場景檔案的旁邊尋找 **SceneName_AcousticParameters.asset** 檔案。 請將您的檔案重新命名以反映新的場景名稱。

## <a name="unity-crashes-when-closing-project"></a>關閉專案時 Unity 當機

在最新版 Unity (2018.2+) 上，有一個 Unity 在您關閉專案時會當機的已知錯誤 (Bug)。 [這個 Unity 問題](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)已追蹤此錯誤。

## <a name="trouble-deploying-to-android"></a>無法部署到 Android
若要在 Android 上使用 Project Acoustics，請將您的建置目標變更為 Android。 Unity 的一些版本有 bug，以部署音訊的外掛程式。 請確定您未使用受到版本[這個 bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>我收到 'could not find metadata file System.Security.dll' (找不到中繼資料檔案 System.Security.dll) 的錯誤

請確定播放器設定中的 [Scripting Runtime Version] \(指令碼處理執行階段版本\) 已設定為 [.NET 4.x Equivalent] \(.NET 4.x 對等\)，然後重新啟動 Unity。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>我在連線至 Azure 時發生驗證問題

請仔細檢查您用於 Auzre 帳戶的認證是否正確、您的帳戶是否支援製作項目中所要求的節點類型，以及您的系統時鐘是否正確。

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>取消製作會使 [製作] 索引標籤處於「正在刪除」狀態
專案樂器會清除所有的 Azure 資源上成功完成或取消作業。 這可能需要最多 5 分鐘的時間。

## <a name="next-steps"></a>後續步驟
* 請嘗試[Unity](unity-quickstart.md)或是[Unreal](unreal-quickstart.md)範例內容

