---
title: 快速入門：如何以 Java 辨識語音 (Windows 或 Linux)
titleSuffix: Microsoft Cognitive Services
description: 了解如何以 Java 辨識語音 (Windows 或 Linux)
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234190"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>快速入門：如何以 Java 辨識語音 (Windows 或 Linux)

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文件說明如何為 Java Run-Time Environment (JRE) 建立運用「語音 SDK」的 Java 型主控台應用程式。
此應用程式會以「Microsoft 認知服務 SDK Maven 套件」為基礎。
我們使用 Eclipse 作為「整合式開發環境」(IDE)。

## <a name="prerequisites"></a>必要條件

* 語音服務適用的訂用帳戶金鑰。 請參閱[免費試用語音服務](get-started.md)。
* 一部能夠執行 Eclipse 且配備可正常運作之麥克風的電腦 (Windows x64、Ubuntu 16.04 x64)。
* 適用於 Java 8 的 64 位元 JRE/JDK。
* 4.8 版 [Eclipse](https://www.eclipse.org)(64 位元版本)。
* 在 Ubuntu 16.04 上，執行下列命令來安裝必要的套件：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>建立及設定您的專案

1. 啟動 Eclipse。

1. 在 Eclipse Launcher 中，於 [Workspace] \(工作區\) 欄位中輸入新目錄的名稱。
   然後按一下 [Launch] \(啟動\)。

   ![建立 Eclipse 工作區](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 不久之後，Eclipse IDE 的主要視窗就會出現。
   如果當中有 [Welcome] \(歡迎使用\) 畫面，請將它關閉。

1. 選取 [File] \(檔案\) \> [New] \(新增\) \> [Project] \(專案\)。

1. 在顯示的 [New Project] \(新增專案\) 精靈中，選取 [Java Project] \(Java 專案\)，然後按 [Next] \(下一步\)。

   ![選取一個精靈](media/sdk/qs-java-jre-02-select-wizard.png)

1. 在下一個視窗中，輸入 **quickstart** 作為專案名稱，然後選擇 [JavaSE-1.8] (或更新版本) 作為執行環境。
   按一下 [完成]。

   ![選取一個精靈](media/sdk/qs-java-jre-03-create-java-project.png)

1. 如果出現標題為 [Open Associated Perspective?] \(是否要開啟相關的透視圖？\) 的快顯視窗，請選取 [Open Perspective] \(開啟透視圖\)。

1. 在 [Package explorer] \(套件總管\) 中的 [quickstart] 專案上按一下滑鼠右鍵，然後選取 [Configure] \(設定\) \> [Convert to Maven Project] \(轉換成 Maven 專案\)。

   ![轉換成 Maven 專案](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. 在快顯示窗中，輸入 **com.microsoft.cognitiveservices.speech.samples** 作為 [Group Id] \(群組識別碼\)，並輸入 **quickstart** 作為 [Artifact Id] \(成品識別碼\)。選取 [完成]。

   ![設定 Maven POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. 編輯 **pom.xml** 檔案。

  * 在檔案結尾的 `</project>` 結尾標記之前，建立一個參考「語音 SDK」之 Maven 存放庫的存放庫區段：

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * 此外，也在其後面新增一個以 0.6.0 版「語音 SDK」作為相依性的相依性區段：

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * 儲存變更。

## <a name="add-the-sample-code"></a>新增範例程式碼

1. 選取 [File] \(檔案\) \> [New] \(新增\) \> [Class] \(類別\)，將新的空白類別新增到您的專案中。

1. 在 [New Java Class] \(新增 Java 類別\) 視窗中，於 [Package] \(套件\) 欄位中輸入 **speechsdk.quickstart**，並在 [Name] \(名稱\) 欄位中輸入 **Main**。

   ![建立 Main 類別](media/sdk/qs-java-jre-06-create-main-java.png)

1. 使用下列程式碼片段來取代 `Main.java` 中的所有程式碼：

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](regions.md)取代 `YourServiceRegion` 字串。

1. 儲存專案的變更。

## <a name="build-and-run-the-sample"></a>建置並執行範例

按 F11 鍵，或選取 [Run] \(執行\) \> [Debug] \(偵錯\)。
系統將會辨識接下來 15 秒來自您麥克風的語音輸入，並記錄在主控台視窗中。

![成功辨識後的主控台輸出](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-jre` 資料夾中尋找此範例。

## <a name="next-steps"></a>後續步驟

* [取得我們的範例](speech-sdk.md#get-the-samples)
