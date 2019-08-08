---
title: 聲場專案 Unreal 和 Wwise 整合
titlesuffix: Azure Cognitive Services
description: 本 how to 說明如何將聲場專案 Unreal 和 Wwise 外掛程式整合到您的專案中。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706629"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>聲場專案 Unreal 和 Wwise 整合
本 how to 提供聲場專案外掛程式套件的詳細整合步驟至您現有的 Unreal 和 Wwise 遊戲專案。 

軟體需求：
* [Unreal 引擎](https://www.unrealengine.com/)4.20 或4.21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1。\*
* [適用于 Unreal 的 Wwise 外掛程式](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * 如果您要使用 Wwise SDK 的直接整合, 而不是使用 Wwise Unreal 外掛程式, 請參閱聲場專案 Unreal 外掛程式並調整 Wwise API 呼叫。

如果您想要搭配 Wwise 以外的音訊引擎使用聲場專案, 請在[聲場專案討論論壇](https://github.com/microsoft/ProjectAcoustics/issues)上提出增強要求。 您可以使用聲場專案 Unreal 外掛程式來查詢聲場資料, 然後對您的引擎進行 API 呼叫。

## <a name="download-project-acoustics"></a>下載聲場專案
如果您還沒有這麼做, 請下載[聲場專案 Unreal & Wwise 外掛程式套件](https://www.microsoft.com/download/details.aspx?id=58090))。 

我們已在套件中包含 Unreal 引擎外掛程式和 Wwise 混音器外掛程式。 Unreal 外掛程式提供編輯器和執行時間整合。 在遊戲期間, 聲場專案 Unreal 外掛程式會針對每個畫面格的每個遊戲物件計算參數, 例如遮蔽。 這些參數會轉譯為 Wwise API 呼叫。

## <a name="review-integration-steps"></a>審查整合步驟

安裝套件並將其部署在遊戲中的主要步驟如下。
1. 安裝聲場專案 Wwise 混音器外掛程式
2. (Re) 將 Wwise 部署到您的遊戲。 此步驟會將混音器外掛程式傳播至您的遊戲專案。
3. 將聲場專案 Unreal 外掛程式新增至您的遊戲
4. 擴充 Wwise 的 Unreal 外掛程式功能
5. 已啟用組建遊戲和檢查 Python
6. 設定 Wwise 專案以使用聲場專案
7. Unreal 中的音訊設定

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1.安裝聲場專案混音器外掛程式
* 開啟 Wwise 啟動器，然後在 [Plugins] \(外掛程式\) 索引標籤的 [Install New Plugins] \(安裝新外掛程式\) 之下，選取 [Add From Directory] \(從目錄新增\)。 

    ![在 Wwise 啟動器中安裝外掛程式的螢幕擷取畫面](media/wwise-install-new-plugin.png)

* 選擇包含在您所下載之套件中的 `AcousticsWwisePlugin\ProjectAcoustics` 目錄。 它包含 Wwise 混音器外掛程式配套。

* Wwise 將會安裝外掛程式。 聲場專案現在應該會顯示在 Wwise 的已安裝外掛程式清單中。
![聲場專案安裝後 Wwise 已安裝之外掛程式清單的螢幕擷取畫面](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2.(Re) 將 Wwise 部署到您的遊戲
即使您已經整合了 Wwise, 也請將 Wwise 重新部署到您的遊戲。 這會挑選聲場專案的 Wwise 外掛程式。

* **引擎外掛程式:** 如果您已在 Unreal C++專案中將 Wwise 安裝為遊戲外掛程式, 請略過此步驟。 如果它是以引擎外掛程式的形式安裝, 例如因為您的 Unreal 專案只是藍圖, 則使用混合外掛程式的 Wwise 部署會更複雜。 建立虛擬的空白 Unreal C++專案, 如果 Unreal 編輯器開啟, 請將它關閉, 然後遵循其餘程式將 Wwise 部署到這個虛擬專案。 然後複製已部署的 Wwise 外掛程式。
 
* 從 Wwise 啟動器，按一下 [Unreal Engine] 索引標籤，然後按一下 [Recent Unreal Engine Projects] \(最近使用的 Unreal Engine 專案\) 旁邊的漢堡功能表，並選取 [Browse for project] \(瀏覽專案\)。 開啟遊戲的 Unreal 專案`.uproject`檔。

    ![Wwise 啟動器的 [Unreal] 索引標籤的螢幕擷取畫面](media/wwise-unreal-tab.png)

* 然後按一下 [**在專案中整合 Wwise** ] 或 [**修改專案中的 Wwise**]。 這個步驟 (re) 會將 Wwise 二進位檔整合到您的專案中, 現在包括聲場專案混音器外掛程式。

* **引擎外掛程式:** 如果您使用 Wwise 作為引擎外掛程式並建立上述的虛擬專案, 請複製 Wwise 已部署的資料夾: `[DummyUProject]\Plugins\Wwise`並將它貼上。 `[UESource]\Engine\Plugins\Wwise` `[DummyUProject]`是空的 Unreal C++專案路徑, 而且`[UESource]`是您已安裝 Unreal 引擎來源的位置。 複製完成之後, 您就可以刪除虛擬專案。

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3.將聲場專案 Unreal 外掛程式新增至您的遊戲
 
* 複製外掛程式`Unreal\ProjectAcoustics`套件中的資料夾, 並建立新的資料夾`[UProjectDir]\Plugins\ProjectAcoustics`, 其中`UProjectDir` `.uproject`是您遊戲的專案資料夾, 其中包含檔案。
  * **引擎外掛程式**:如果您使用 Wwise 做為引擎外掛程式, 則也應該使用聲場專案做為 Unreal 引擎外掛程式。 請改用: `[UESource]\Engine\Plugins\ProjectAcoustics`, 而不是上述的目的地目錄。

* 確認您在`ProjectAcoustics`資料夾`Wwise`旁看到資料夾。 它包含 Wwise 外掛程式, 以及您在上述步驟2中部署之混音器外掛程式的二進位檔。

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4.擴充 Wwise 的 Unreal 外掛程式功能
* 聲場專案 Unreal 外掛程式需要根據[這些指導方針](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html), 從 Wwise UNREAL 外掛程式 API 公開額外的行為。 我們已包含批次檔, 以將修補程式自動化。 
* 在 `Plugins\ProjectAcoustics\Resources` 內，執行 `PatchWwise.bat`。 以下範例影像使用我們的 AcousticsGame 範例專案。

    ![反白顯示提供的腳本以修補 Wwise 的 Windows Explorer 視窗螢幕擷取畫面](media/patch-wwise-script.png)

* 如果您沒有安裝 DirectX SDK，便需要將 `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`中包含 DXSDK_DIR 的程式碼行註解化

    ![顯示已註解排除 DXSDK 的程式碼編輯器螢幕擷取畫面](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5.已啟用組建遊戲和檢查 Python

* 編譯您的遊戲, 並確保它能正確建立。 否則, 請先仔細檢查先前的步驟, 再繼續進行。 
* 在 [Unreal 編輯器] 中開啟您的專案。 
* **引擎外掛程式:** 如果使用 ProjectAcoustics as engine 外掛程式, 也請確定它已啟用, 並列在 [內建] 外掛程式之下。
* 您應該會看到新的模式, 表示已整合聲場專案。

    ![顯示聲場模式已滿的 Unreal 螢幕擷取畫面](media/acoustics-mode-full.png)

* 確認您已啟用適用于 Unreal 的 Python 外掛程式。 若要讓編輯器整合正常運作, 這是必要的。

    ![在 Unreal 編輯器中啟用 Python 擴充功能的螢幕擷取畫面](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6.Wwise 專案設定

範例 Wwise 專案隨附于範例下載。 我們建議您與這些指示一起查看。 下列螢幕擷取畫面取自此專案。

### <a name="bus-setup"></a>匯流排設定
* 聲場專案 Unreal 外掛程式會在具有此***確切***名稱的匯流排上尋找相關聯的混音器外掛程式`Project Acoustics Bus`:。 使用此名稱建立新的音訊匯流排。 混音器外掛程式可以在各種設定中工作, 但現在我們假設它只會用來執行「回音處理」。 此匯流排會針對所有使用聲場的來源攜帶混合的回音信號。 它可以將上游混合成任何匯流排混合結構, 如下所示, 取自範例下載所包含的 Wwise 範例專案。

    ![顯示聲場專案匯流排的 Wwise 匯流排螢幕擷取畫面](media/acoustics-bus.png)

* 匯流排上的通道設定必須設為下列其中一個: `1.0, 2.0, 4.0, 5.1 or 7.1`。 其他的則會導致此匯流排上沒有輸出。

    ![聲場專案匯流排通道設定選項的螢幕擷取畫面](media/acoustics-bus-channel-config.png)

* 現在請移至聲場專案匯流排詳細資料, 並確定您可以看到 [混音器外掛程式] 索引標籤

    ![Wwise 的螢幕擷取畫面, 顯示如何啟用 [聲場專案匯流排的混音器外掛程式] 索引標籤](media/mixer-tab-enable.png)

* 然後移至 [混音器外掛程式] 索引標籤, 並將聲場專案混音器外掛程式新增至匯流排

    ![Wwise 匯流排的 Screenshow, 顯示如何新增聲場專案混音器外掛程式](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>動作專案-混音器階層設定
* 基於效能考慮, 聲場專案會同時將音訊 DSP 套用至所有來源。 這需要外掛程式以混音器外掛程式的身分運作。 Wwise 需要在輸出匯流排上有混音器外掛程式, 但輸出匯流排通常會攜帶幹輸出信號。 聲場專案需要在上`Project Acoustics Bus`攜帶濕信號時, 透過 aux 匯流排路由處理信號。 下列處理常式支援對此信號流程進行漸進式遷移。

* 假設您有一個具有動作專案混音器階層的現有專案, 其中包含項措施師法、武器和其他層級。 每個都有相對應的輸出匯流排來進行其試混搭。 讓我們假設您想要遷移項措施師法以使用聲場。 首先建立對應的 aux 匯流排, 以攜帶其 submix, 這是項措施師法輸出匯流排的子系。 比方說, 我們在下圖中使用了「晾乾」前置詞來組織這些, 雖然確切名稱並不重要。 您在項措施師法匯流排上所擁有的任何計量或效果仍會如以往運作。

    ![建議的 Wwise 試混合設定螢幕擷取畫面](media/wwise-dry-mix-setup.png)

* 接著, 修改項措施師法動作專案-混音器的匯流排輸出結構, 如下所示, 並將聲場專案匯流排設定為輸出匯流排, 並將 Dry_Footsteps 設定為使用者定義的 aux 匯流排。

    ![建議的 Wwise 動作專案混音器匯流排設定螢幕擷取畫面](media/actor-mixer-bus-settings.png)

* 現在, 所有項措施師法都會收到聲場處理, 並將其在聲場專案匯流排上的回音輸出。 試信號會如往常般透過 Dry_Footsteps 和 hrtf 路由傳送。

* 聲場專案只適用于世界上有3D 位置的音效。 遵循[Wwise 檔](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), 必須設定位置屬性, 如下所示。 「3D Spatialization」設定可以是「位置」或「位置 + 方向」 (視需要)。

    ![建議的 Wwise 動作專案定位設定的螢幕擷取畫面](media/wwise-positioning.png)

* 將輸出匯流排設定為其他將上游混合成**聲場專案匯流排**的匯流排將無法使用。 Wwise 會在混音器外掛程式上施加這項需求。

* 如果您想要讓項措施師法動作專案混音器階層中的子系不使用聲場, 您可以隨時在其上使用「覆寫父系」來退出宣告。

* 如果您在遊戲中的任何動作專案混音器上使用遊戲或使用者定義的傳送來進行回音, 請在此動作專案-混音器上關閉這些專案, 以避免套用「回音」兩次。

### <a name="spatialization"></a>Spatialization
根據預設, 聲場專案 Wwise 混音器外掛程式會套用卷加的回音, 讓 Wwise 執行移動流覽 spatialization。 使用此預設僅限限時設定中的聲場專案時, 您可以自由地在您的近乎混合上使用任何通道設定和 spatialization 方法, 讓您混合和比對幾乎任何具有聲場專案「回音」的空間定位器。 您的選項包括以[Ambisonics 為基礎的雙聲道 spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
聲場專案包含支援以物件為基礎之高解析度 HRTF 轉譯和移動流覽的選擇性空間定位器。 核取 [混音器外掛程式設定] 上的 [執行 Spatialization] 核取方塊, 然後選擇 [HRTF] 或 [移動], 並停用在上述所有試匯流排上設定的使用者定義的 aux 傳送, 以避免 spatializing 兩次, 同時聲場專案混音器外掛程式和 Wwise。 無法即時變更 spatialization 模式, 因為它需要重新產生音效。 您必須重新開機 Unreal, 然後在叫用播放之前重新產生 soundbanks, 以挑選混音器外掛程式設定變更, 例如 [執行 Spatialization] 核取方塊。

![Wwise 混音器外掛程式 Spatialization 設定的螢幕擷取畫面](media/mixer-spatial-settings.png)

可惜的是, 目前無法支援其他以物件為基礎的空間定位器外掛程式, 因為它們會實作為混音器外掛程式, 而 Wwise 目前不允許將多個混音器外掛程式指派給單一動作專案混音器。  

## <a name="7-audio-setup-in-unreal"></a>7.Unreal 中的音訊設定
* 首先, 您必須製作您的遊戲層級來產生聲場資產, 這會放入`Content\Acoustics`中。 請參閱[Unreal 製作教學](unreal-baking.md)課程, 並繼續這裡。 範例套件中包含一些預先內建的層級。
* 在您的場景中建立聲場空間動作專案。 請只在層級建立其中一個執行者, 因為它代表整個層級的聲場。 

    ![Unreal 編輯器的螢幕擷取畫面, 其中顯示聲場空間執行者的建立](media/create-acoustics-space.png)

* 現在將內建聲場資料資產指派給聲場空間動作專案上的聲場資料插槽。 您的場景現在已經聲場了!

    ![Unreal editor s howing 聲場資產指派的螢幕擷取畫面](media/acoustics-asset-assign.png)

* 現在新增空白的執行者, 並執行下列動作:

    ![Unreal 編輯器的螢幕擷取畫面, 顯示空白執行者中的聲場元件使用量](media/acoustics-component-usage.png)

1. 將聲場音訊元件新增至動作專案。 此元件會使用聲場專案的功能來擴充 Wwise 音訊元件。
2. 預設會核取 [播放開始] 方塊, 這會在層級啟動時觸發相關聯的 Wwise 事件。
3. 使用 [顯示聲場參數] 核取方塊, 列印與來源有關的螢幕上的調試資訊。
    ![已啟用 debug 值的音效來源上 Unreal 編輯器聲場面板的螢幕擷取畫面](media/debug-values.png)
4. 根據平常的 Wwise 工作流程指派 Wwise 事件
5. 請確定已關閉 [使用空間音訊]。 此時, 如果您使用特定音訊元件的聲場專案, 就無法同時使用 Wwise 的空間音訊引擎來進行聲場。

您全都準備好了。 四處移動場景並探索聲場效應!

## <a name="next-steps"></a>後續步驟
* Unreal/Wwise 中的聲場專案[設計](unreal-workflow.md)教學課程
* [瞭解如何](unreal-baking.md)為您的遊戲場景進行 bake 
