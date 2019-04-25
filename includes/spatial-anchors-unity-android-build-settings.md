---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012111"
---
藉由選取 [檔案] > [組建設定] 來開啟 [組建設定]。

在 [平台] 區段中，選取 [Android]。 將 [組建系統] 變更為 [Gradle]，然後選取 [匯出專案]。

選取 [切換平台]，將平台變更為 [Android]。 Unity 可能會提示您安裝 Android 支援元件 (如果元件遺失的話)。

![Unity 組建設定視窗](./media/spatial-anchors-unity/unity-android-build-settings.png)

關閉 [組建設定] 視窗。

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>下載並匯入適用於 Unity 的 ARCore SDK

從 [ARCore SDK for Unity 1.7 版本](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0)下載 `unitypackage` 檔案。 回到 Unity 專案，選取 [資產] > [匯入套件] > [自訂套件]，然後選取您先前下載的 `unitypackage` 檔案。 在 [匯入 Unity 套件] 對話方塊中，確認已選取所有檔案，然後選取 [匯入]。
