---
title: 專案樂器 Unreal 和 Wwise 整合
titlesuffix: Azure Cognitive Services
description: 此操作說明描述整合到您的專案的專案樂器 Unreal 和 Wwise 外掛程式。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 19565ef239ba3ea1f791f80e4599a63b944c491b
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137884"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>專案樂器 Unreal 和 Wwise 整合
此操作說明提供詳細的整合步驟的專案樂器外掛程式封裝到您現有的 Unreal 和 Wwise 遊戲專案。 

軟體需求：
* [Unreal Engine](https://www.unrealengine.com/) 4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1。 +
* [Unreal Wwise 外掛程式](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * 如果您使用 Wwise SDK 直接整合，而不使用 Wwise Unreal 外掛程式，請參閱專案樂器 Unreal 外掛程式，並調整 Wwise API 呼叫。

如果您想要使用的音訊引擎 Wwise 以外的專案樂器上, 與我們連絡[專案樂器論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)。 您可以使用專案樂器 Unreal 外掛程式來查詢樂器資料，然後再進行 API 呼叫，以便您的引擎。

## <a name="download-project-acoustics"></a>下載專案樂器
如果您還沒有這麼做，下載[專案樂器外掛程式封裝](https://www.microsoft.com/download/details.aspx?id=57346))。 

在封裝中，我們已加入的 Unreal Engine 外掛程式和 Wwise 混音器 」 外掛程式。 Unreal 外掛程式提供編輯器和執行階段的整合。 期間的遊戲，Unreal 專案樂器外掛程式會計算每個畫面參數，例如針對每個遊戲的物件會受阻擋。 這些參數會轉譯成 Wwise API 呼叫。

## <a name="review-integration-steps"></a>檢閱整合步驟

有這些安裝套件，並將它部署在您的遊戲中的主要步驟。
1. 安裝專案樂器 Wwise 混音器外掛程式
2. （重新） 將 Wwise 部署您的遊戲。 此步驟會傳播到遊戲專案混音器 」 外掛程式。
3. 將專案樂器 Unreal 外掛程式加入您的遊戲
4. 擴充 Wwise 的 Unreal 外掛程式功能
5. 建置遊戲，並確認已啟用 Python
6. 若要使用專案樂器將 Wwise 專案設定
7. Unreal 中的音訊設定

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1.安裝專案樂器混音器外掛程式
* 在中，然後開啟 Wwise 啟動器**外掛程式**索引標籤之下**安裝新的外掛程式**，選取**新增從目錄**。 

    ![安裝 Wwise 外掛程式](media/wwise-install-new-plugin.png)

* 選擇`AcousticsWwisePlugin\ProjectAcoustics`下載的封裝中包含的目錄。 它包含 Wwise mixer 外掛程式套件組合。

* Wwise 將安裝的外掛程式。 專案樂器應該現在會顯示在 已安裝的外掛程式清單中 Wwise 中。
![U E 整合後續 Mixer 外掛程式安裝](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2.（重新） 將 Wwise 部署到您的遊戲
即使您已整合 Wwise，重新部署至您的遊戲 Wwise。 這會挑選專案樂器 Wwise 外掛程式。

* **引擎外掛程式：** 如果您有安裝 Unreal c + + 專案中的遊戲外掛程式為 Wwise，略過此步驟。 如果執行個體因為您的 Unreal 專案是使用我們的混音器 」 外掛程式藍圖，Wwise 部署是更複雜，它會改為引擎外掛程式安裝。 建立空的、 空的 Unreal c + + 專案、 關閉它，如果 Unreal 編輯器隨即開啟，並遵循其餘的程序，來將 Wwise 部署到此虛擬的專案。 然後複製已部署的 Wwise 外掛程式。
 
* 從 Wwise 啟動程式中，按一下**Unreal Engine**索引標籤，然後按一下 [漢堡] 功能表下一步**最近使用的 Unreal Engine 專案**，然後選取**瀏覽專案**。 開啟您的遊戲 Unreal 專案`.uproject`檔案。

    ![Wwise Unreal 索引標籤](media/wwise-unreal-tab.png)

* 然後按一下**專案中的整合 Wwise**或是**專案中的修改 Wwise**。 （重新） 的這個步驟會整合到您的專案，現在還包含了專案樂器 mixer 外掛程式 Wwise 二進位檔。

* **引擎外掛程式：** 如果您使用 Wwise 為引擎外掛程式，並建立與上述的虛設專案複製資料夾部署的 Wwise:`[DummyUProject]\Plugins\Wwise`並貼上`[UESource]\Engine\Plugins\Wwise`。 `[DummyUProject]` 是空的 Unreal c + + 專案路徑，以及`[UESource]`是當您有安裝 Unreal 引擎來源。 一旦您完成複製，您可以刪除空的專案。

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3.將專案樂器 Unreal 外掛程式加入您的遊戲
 
* 複製`Unreal\ProjectAcoustics`資料夾中的外掛程式封裝，然後建立新的資料夾`[UProjectDir]\Plugins\ProjectAcoustics`，其中`UProjectDir`您的遊戲專案資料夾包含`.uproject`檔案。
  * **引擎外掛程式**:如果您使用 Wwise 作為引擎外掛程式，您應該使用專案樂器為的 Unreal engine 外掛程式。 除了上述的目的地目錄中，使用： `[UESource]\Engine\Plugins\ProjectAcoustics`。

* 確認您看到`Wwise`資料夾旁邊`ProjectAcoustics`資料夾。 它包含 Wwise 外掛程式，以及 mixer 外掛程式 （re-） 部署上述的步驟 2 中的二進位檔。

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4.擴充 Wwise 的 Unreal 外掛程式功能
* Unreal 專案樂器外掛程式需要額外的行為會公開從 Wwise Unreal 外掛程式 API，以每個[這些指導方針](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)。 我們已加入批次檔自動化修補程序。 
* 內部`Plugins\ProjectAcoustics\Resources`，請執行`PatchWwise.bat`。 下面的範例圖像中，會使用我們的 AcousticsGame 範例專案。

    ![修補程式 Wwise 指令碼](media/patch-wwise-script.png)

* 如果您沒有安裝 DirectX SDK，您將需要標記為註解包含 DXSDK_DIR 中的行 `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![DXSDK 標記為註解](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5.建置遊戲，並確認已啟用 Python

* 編譯您的遊戲，並確保正確建置。 否則，請檢查先前的步驟，請仔細再繼續進行。 
* 在 「 Unreal 編輯器 」 中開啟您的專案。 
* **引擎外掛程式：** 如果使用 ProjectAcoustics 做為引擎外掛程式，也請確定已啟用，列在 「 內建 」 外掛程式。
* 您應該會看到新的模式，這表示專案樂器已整合。

    ![樂器建立完整的模型](media/acoustics-mode-full.png)

* 請確認您的 Unreal 啟用 Python 外掛程式。 這是必要的編輯器整合才能正常運作。

    ![請確定 Python](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6.Wwise 專案安裝程式

包含範例下載的範例 Wwise 專案。 我們建議看看它與這些指示。 以下螢幕擷取畫面取自此專案。

### <a name="bus-setup"></a>匯流排設定
* 與這個匯流排上的相關聯的混音器外掛程式會尋找專案樂器 Unreal 外掛程式***確切***名稱： `Project Acoustics Bus`。 使用此名稱建立新的音訊匯流排。 Mixer 外掛程式可以搭配各種組態，但現在我們假設它將會用來執行僅殘響器處理。 此匯流排會執行使用樂器的所有來源的混合殘響器訊號。 它可以混合使用上游至混用結構任何匯流排，範例如下所示，取自範例下載中包含我們 Wwise 範例專案。

    ![樂器匯流排](media/acoustics-bus.png)

* 在匯流排上的通道設定必須設定為其中一個： `1.0, 2.0, 4.0, 5.1 or 7.1`。 其他設定 將會導致此匯流排上沒有輸出。

    ![樂器匯流排](media/acoustics-bus-channel-config.png)

* 現在，移至專案樂器匯流排的詳細資訊，並確保您可以看到 [Mixer 外掛程式] 索引標籤

    ![樂器匯流排](media/mixer-tab-enable.png)

* 請移至 [Mixer 外掛程式] 索引標籤，並將專案樂器 mixer 外掛程式新增至匯流排

    ![新增 Mixer 外掛程式](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>動作項目 mixer 階層安裝程式
* 基於效能考量，專案樂器適用於音訊 DSP 所有來源同時。 這需要做的混音器外掛程式的外掛程式。 雖然輸出匯流排通常是執行 dry 輸出訊號 Wwise 仍必須要在輸出匯流排上的混音器外掛程式。 專案樂器需要透過 aux 匯流排上進行路由 dry 訊號，而濕的訊號附在`Project Acoustics Bus`。 下列程序支援漸進式移轉到此訊號流程。

* 假設您有現有的專案包含最上層的腳步、 武器和其他人的動作項目 mixer 階層。 每個都有其 dry mix 的對應輸出匯流排。 可讓您想要移轉使用樂器的腳步的假設。 首先，建立對應的 aux 匯流排，來執行子系的腳步輸出匯流排其 dry submix。 比方說，我們使用 「 Dry"的前置詞在下列影像中組織這些項目，雖然的確切名稱並不重要。 任何計量或您有腳步匯流排的效果仍舊會和以前一樣。

    ![Wwise Dry 混用安裝程式](media/wwise-dry-mix-setup.png)

* 然後，如下所示修改腳步動作項目 mixer 匯流排輸出結構，其設定為輸出匯流排和 Dry_Footsteps 專案樂器匯流排設定為使用者定義的 aux 匯流排。

    ![安裝程式-Mixer 匯流排 Wwise 動作項目](media/actor-mixer-bus-settings.png)

* 現在所有的腳步取得樂器處理方式，並輸出其殘響器專案樂器匯流排上。 Dry 訊號是透過 Dry_Footsteps 路由傳送，並如往常般 spatialized。

* 專案樂器僅適用於有 3D 位置的世界中的音效。 遵循[Wwise 文件](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)，則必須設定定位屬性，如所示。 視需要 「 位置 」 或 「 位置 + 方向 」 可以是 「 3D 空間 」 設定。

    ![Wwise 動作項目位置的設定](media/wwise-positioning.png)

* 將輸出匯流排設定為上游混合成一些其他匯流排**專案樂器匯流排**將無法運作。 Wwise 加諸在 mixer 外掛程式上的這項需求。

* 如果您想要不使用 樂器的腳步動作項目 mixer 階層中的子系，您可以一律使用上面的 「 覆寫父代 」 它退出。

* 如果您將在遊戲中任何動作項目 mixer 殘響器遊戲或使用者定義的傳送，那些上關閉以避免套用殘響器兩次這個動作項目混音器。

### <a name="spatialization"></a>空間
根據預設，專案樂器 Wwise mixer 外掛程式適用於迴旋殘響器、 離開 Wwise 要移動的空間。 時使用此預設僅限殘響器組態中的專案樂器，您可以隨意使用任何通道組態和往方法在您 dry 的組合，可讓您混合及比對專案樂器殘響器使用幾乎任何空間。 您的選項包括[Ambisonics 型 binaural spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)並[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
專案樂器包含選擇性的空間可支援物件為基礎高解析度 HRTF 轉譯和移動瀏覽。 檢查 mixer 外掛程式設定的 「 執行空間 」 核取方塊並選擇 HRTF 或移動或停用設定上述所有 dry 匯流排上以避免兩次，spatializing 專案樂器 mixer 外掛程式 Wwise 是以使用者定義的輔助裝置傳送。 無法即時變更往模式，因為它需要音效銀行重新產生。 您必須重新啟動 Unreal，然後重新 soundbanks 產生才按下 [播放]，挑選 mixer 外掛程式組態變更，例如執行往核取方塊。

![Mixer 外掛程式空間設定](media/mixer-spatial-settings.png)

不幸的是，其他物件為基礎的空間外掛程式不支援這一次它們被實作為混音器 」 外掛程式，以及 Wwise 目前不允許多個指派給單一動作項目 mixer 的混音器 」 外掛程式。  

## <a name="7-audio-setup-in-unreal"></a>7.Unreal 中的音訊設定
* 首先您必須修正您遊戲的層級，以產生樂器資產，其會置於`Content\Acoustics`。 請參閱[Unreal 試吃的教學課程](unreal-baking.md)和這裡繼續。 範例封裝中包含一些預先烤的層。
* 場景中建立樂器空間動作項目。 只能建立其中一個這些動作項目層級中，它代表整個層級樂器。 

    ![建立樂器的空間](media/create-acoustics-space.png)

* 現在將烤原音資料資產指派至樂器資料位置的樂器空間動作項目。 場景現在有樂器 ！

    ![指派樂器資產](media/acoustics-asset-assign.png)

* 現在加入空白的動作項目，並執行下列作業：

    ![樂器元件的使用方式](media/acoustics-component-usage.png)

1. 樂器音效將元件新增至動作項目。 此元件擴充功能的專案樂器 Wwise 音訊元件。
2. 在 [開始] 方塊上的播放會檢查根據預設，會觸發在層級的啟動相關聯的 Wwise 事件。
3. 使用顯示樂器參數的核取方塊螢幕上列印偵錯來源的相關資訊。
    ![偵錯值](media/debug-values.png)
4. 每個一般 Wwise 工作流程 Wwise 事件指派
5. 請確定使用的空間音訊已關閉。 在此階段中，如果您使用專案樂器針對特定的音訊元件，您不能同時使用 Wwise 的空間音訊引擎樂器。

您全都準備好了。 場景中四處移動，並探索柔和式效果 ！

## <a name="next-steps"></a>後續步驟
* [設計](unreal-workflow.md)Unreal/Wwise 中的專案樂器的教學課程
* [了解如何執行 bake](unreal-baking.md)針對您的遊戲場景 
