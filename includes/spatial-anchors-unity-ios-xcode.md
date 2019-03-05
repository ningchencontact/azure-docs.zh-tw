---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b802c9dbd0cef65325cb03538b68b49c57b85bb3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56890967"
---
選取 [組建] 以開啟對話方塊。 然後，選取要匯出 Xcode 專案的資料夾。

匯出完成時，資料夾會顯示出已包含匯出的 Xcode 專案。

### <a name="open-the-xcode-project"></a>開啟 Xcode 專案

在匯出的 Xcode 專案資料夾中，執行下列命令以安裝專案所需的 CocoaPods：

```bash
pod install --repo-update
```

現在您可以開啟 `Unity-iPhone.xcworkspace`，以在 Xcode 中開啟專案：

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> 如果您看到 `library not found for -lPods-Unity-iPhone` 錯誤，您可能開到 `.xcodeproj` 檔案，而不是 `.xcworkspace`。 開啟 `.xcworkspace`，然後再試一次。

選取根 **Unity-iPhone** 節點以檢視專案設定，然後選取 [一般] 索引標籤。

在 [簽署] 底下選取 [自動管理簽署]。 在用來重設組建設定的對話方塊中選取 [自動啟用]。

在 [部署資訊] 底下，確定 [部署目標] 已設定為 `11.0`。

### <a name="deploy-the-app-to-your-ios-device"></a>將應用程式部署到您的 iOS 裝置

將 iOS 裝置連接至 Mac，並將 [作用中配置] 設定為您的 iOS 裝置。

![選取裝置](./media/spatial-anchors-unity/select-device.png)

選取 [建置然後執行目前的配置]。

![部署並執行](./media/spatial-anchors-unity/deploy-run.png)