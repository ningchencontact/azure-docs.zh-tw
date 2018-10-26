---
title: Project Acoustics 外掛程式的已知問題
titlesuffix: Azure Cognitive Services
description: 使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。
services: cognitive-services
author: kylestorck
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 6d3605b579a44dccb259bef281392cbfe2b9f916
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902143"
---
# <a name="known-issues"></a>已知問題
使用 Project Acoustics 的「設計人員預覽」時，您可能會遇到下列已知問題。

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>將場景重新命名時遺失聲場參數

如果您將場景重新命名，所有屬於該場景的聲場參數都會自動轉移到新場景。 不過，它們將仍存在於舊的資產檔案中。 請在 [Editor] 目錄內您場景檔案的旁邊尋找 **SceneName_AcousticParameters.asset** 檔案。 請將您的檔案重新命名以反映新的場景名稱。

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>執行階段體素的大小與場景預覽體素的大小不同

如果您在 [Probes] \(探查\) 索引標籤上執行**計算**並檢視體素，然後針對相同的場景，在執行階段執行製作並檢視體素，體素的大小就會不同。 製作前顯示的體素是模擬中使用的體素。 在執行階段顯示的體素是用於探查點之間的插補。 這可能會造成實際上未開啟的門戶在執行階段顯示為開啟的不一致情況。

## <a name="unity-crashes-when-closing-project"></a>關閉專案時 Unity 當機

在最新版 Unity (2018.2+) 上，有一個 Unity 在您關閉專案時會當機的已知錯誤 (Bug)。 [這個 Unity 問題](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project)已追蹤此錯誤。

## <a name="trouble-deploying-to-android"></a>無法部署到 Android
若要在 Android 上使用 Project Acoustics，請將您的建置目標變更為 Android。 有些 Unity 版本含有音訊外掛程式部署方面的錯誤 (bug)，請確定您不是使用受到[這個錯誤](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player)影響的版本。

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>我收到 'could not find metadata file System.Security.dll' (找不到中繼資料檔案 System.Security.dll) 的錯誤

請確定播放器設定中的 [Scripting Runtime Version] \(指令碼處理執行階段版本\) 已設定為 [.NET 4.x Equivalent] \(.NET 4.x 對等\)，然後重新啟動 Unity。

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>我在連線至 Azure 時發生驗證問題

請仔細檢查您用於 Auzre 帳戶的認證是否正確、您的帳戶是否支援製作項目中所要求的節點類型，以及您的系統時鐘是否正確。

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>取消製作會使 [製作] 索引標籤處於「正在刪除」狀態
Project Acoustics 將在成功完成或取消作業時清理所有 Azure 資源，這可能最多需要 5 分鐘。

## <a name="next-steps"></a>後續步驟
* 開始[將聲場整合到 Unity 專案中](getting-started.md)

