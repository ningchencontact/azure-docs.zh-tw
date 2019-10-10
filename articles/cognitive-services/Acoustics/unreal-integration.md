---
title: 聲場專案 Unreal 和 Wwise 整合
titlesuffix: Azure Cognitive Services
description: 本文說明如何將聲場專案 Unreal 和 Wwise 外掛程式整合到您的專案中。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243051"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>聲場專案 Unreal 和 Wwise 整合
本文說明如何將聲場專案外掛程式套件整合到您現有的 Unreal 和 Wwise 遊戲專案中。

軟體需求：
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018。1
* [適用于 Unreal 的 Wwise 外掛程式](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  如果您使用 Wwise SDK 的直接整合，而不是 Wwise Unreal 外掛程式，請參閱聲場專案 Unreal 外掛程式並調整 Wwise API 呼叫。

若要搭配 Wwise 以外的音訊引擎使用聲場專案，請在[聲場專案討論論壇](https://github.com/microsoft/ProjectAcoustics/issues)中提出增強要求。 您可以使用聲場專案 Unreal 外掛程式來查詢聲場資料，並對您的引擎進行 API 呼叫。

## <a name="download-project-acoustics"></a>下載聲場專案
下載[聲場專案 Unreal 和 Wwise 外掛程式套件](https://www.microsoft.com/download/details.aspx?id=58090)（如果尚未這麼做）。

封裝中包含 Unreal 引擎外掛程式和 Wwise 混音器外掛程式。 Unreal 外掛程式提供編輯器和執行時間整合。 在遊戲期間，聲場專案 Unreal 外掛程式會針對每個畫面格的每個遊戲物件計算參數，例如遮蔽。 這些參數會轉譯成 Wwise API 呼叫。

## <a name="integration-steps"></a>整合步驟

請遵循下列步驟來安裝套件，並將它部署在您的遊戲中。

### <a name="install-the-project-acoustics-mixer-plug-in"></a>安裝聲場專案混音器外掛程式
1. 開啟 Wwise 啟動器。 在 [**外掛程式**] 索引標籤的 [**安裝新外掛程式**] 底下，選取 [**從目錄新增**]。

    ![在 Wwise 啟動器中安裝外掛程式](media/wwise-install-new-plugin.png)

1. 選取下載套件中的*AcousticsWwisePlugin\ProjectAcoustics*目錄。 其中包含 Wwise 混音器外掛程式套件組合。

   Wwise 將會安裝外掛程式。 聲場專案應該會出現在 Wwise 的 [已安裝的外掛程式] 清單中。  
![安裝聲場專案之後，Wwise 已安裝的外掛程式清單](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>將 Wwise Dedeploy 到您的遊戲
將 Wwise 重新部署到您的遊戲中，即使您已經整合 Wwise 也一樣。 此步驟會整合聲場專案 Wwise 外掛程式。

   > [!NOTE]
   > **引擎外掛程式：** 如果您已在 Unreal C++專案中將 Wwise 安裝為遊戲外掛程式，請略過此步驟。 如果它是以引擎外掛程式的形式安裝，例如因為您的 Unreal 專案只是藍圖，則使用混合外掛程式的 Wwise 部署會更複雜。 建立虛擬空的 Unreal C++專案。 如果 Unreal 編輯器開啟，請關閉它，然後遵循其餘程式將 Wwise 部署到虛擬專案。 然後，複製已部署的 Wwise 外掛程式。
 
1. 從 Wwise 啟動器，選取 [ **Unreal 引擎**] 索引標籤。選取 [**最近使用的 Unreal 引擎專案**] 旁邊的 [漢堡] （圖示）功能表，然後選取 **[流覽專案]** 。 開啟遊戲的*Unreal 專案專案檔。*

    ![[Wwise 啟動器 Unreal] 索引標籤](media/wwise-unreal-tab.png)

1. 選取 [**在專案中整合 Wwise** ] 或 [**修改專案中的 Wwise**]。 此步驟會將 Wwise 二進位檔整合到您的專案中，包括聲場專案混音器外掛程式。

   > [!NOTE]
   > **引擎外掛程式：** 如果您使用 Wwise 作為引擎外掛程式，而且您已建立如先前所述的虛擬專案，請複製 Wwise 已部署的資料夾： *[DummyUProject] \Plugins\Wwise*。 將它貼到 *[UESource] \Engine\Plugins\Wwise*上。 *[DummyUProject]* 是空的 Unreal C++專案路徑，而 *[UESource]* 則是安裝 Unreal 引擎來源的位置。 複製資料夾之後，您可以刪除虛擬專案。

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>將聲場專案 Unreal 外掛程式新增至您的遊戲
 
1. 複製外掛程式套件中的*Unreal\ProjectAcoustics*資料夾。 建立新資料夾 *[UProjectDir] \Plugins\ProjectAcoustics*，其中 *[UProjectDir]* 是包含*uproject*檔案的遊戲專案資料夾。

   > [!NOTE]
   > **引擎外掛程式**：如果您使用 Wwise 做為引擎外掛程式，則也應該使用聲場專案做為 Unreal 引擎外掛程式。 請使用 *[UESource] \Engine\Plugins\ProjectAcoustics*，而不是先前提到的目的地目錄。

1. 確認您在*ProjectAcoustics*資料夾旁看到*Wwise*資料夾。 它包含 Wwise 外掛程式，以及您稍早部署之混音器外掛程式的二進位檔。

### <a name="extend-wwise-unreal-plug-in-functionality"></a>擴充 Wwise Unreal 外掛程式功能
聲場專案 Unreal 外掛程式需要根據[這些指導方針](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)，從 Wwise UNREAL 外掛程式 API 公開額外的行為。 我們已包含批次檔，以將修補程式自動化。

* 在*Plugins\ProjectAcoustics\Resources*中，執行*PatchWwise*。 下列範例影像使用我們的 AcousticsGame 範例專案。

    ![已反白顯示 [修補 Wwise 的腳本] 的 Windows Explorer 視窗](media/patch-wwise-script.png)

* 如果您未安裝 DirectX SDK：根據您所使用的 Wwise 版本而定，您可能需要在*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*中將包含 `DXSDK_DIR` 的那一行標記為批註：

    ![顯示 ' DXSDK ' 的程式碼編輯器已標記為批註](media/directx-sdk-comment.png)

* 如果您使用 Visual Studio 2019 來編譯：若要解決 Wwise 的連結錯誤，請手動將*AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*中的預設 `VSVersion` 值變更為**microsoft.vc150**：

    ![顯示 "VSVersion" 的程式碼編輯器已變更為 "microsoft.vc150"](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>建立遊戲並檢查 Python 是否已啟用

1. 編譯您的遊戲，並確定它已正確建立。 如果未建立，請先仔細檢查先前的步驟，再繼續進行。

1. 在 [Unreal 編輯器] 中開啟您的專案。

    > [!NOTE]
    > **引擎外掛程式：** 如果您使用 ProjectAcoustics 作為引擎外掛程式，也請確定它是在「內建」外掛程式底下啟用。

    您應該會看到新的模式，這表示聲場專案已經整合。

    ![Unreal 中的聲場模式已滿](media/acoustics-mode-full.png)

1. 確認已啟用適用于 Unreal 的 Python 外掛程式，讓編輯器整合正常運作。

    ![已啟用 Unreal 編輯器中的 Python 延伸模組](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>設定 Wwise 專案以使用聲場專案

範例 Wwise 專案包含在範例下載中。 我們建議您將其與這些指示一起查看。 本文稍後的螢幕擷取畫面是來自此專案。

#### <a name="bus-setup"></a>匯流排設定
聲場專案 Unreal 外掛程式會在具有確切名稱 `Project Acoustics Bus` 的匯流排上尋找相關聯的混音器外掛程式。 建立具有相同名稱的新音訊匯流排。 混音器外掛程式可以在各種設定中工作。 但現在我們假設它只會用於回音處理。 此匯流排會針對所有使用聲場的來源攜帶混合的回音信號。 它可以將上游混合成任何匯流排混合結構。 範例會從範例下載所包含的 Wwise 範例專案中顯示。

![顯示聲場專案匯流排的 Wwise 匯流排](media/acoustics-bus.png)

1. 將匯流排上的通道設定設為*1.0*、 *2.0*、 *4.0*、 *5.1*或*7.1*。 任何其他設定都不會產生匯流排上的輸出。

    ![聲場專案匯流排的通道設定選項](media/acoustics-bus-channel-config.png)

1. 移至 [聲場專案匯流排詳細資料]，並確定您可以看到 [**混音器外掛程式**] 索引標籤。

    ![已啟用聲場專案匯流排的 [混音器外掛程式] 索引標籤的 Wwise](media/mixer-tab-enable.png)

1. 移至 [**混音器外掛程式**] 索引標籤，並將聲場專案混音器外掛程式新增至匯流排。

    ![如何將聲場專案混音器外掛程式新增至 Wwise 匯流排的圖表](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>動作專案-混音器階層設定
為了達到最佳效能，聲場專案會同時將音訊數位信號處理套用至所有來源。 因此，外掛程式必須以混音器外掛程式的形式運作。 Wwise 需要在輸出匯流排上有混音器外掛程式，不過輸出匯流排通常會攜帶幹輸出信號。 聲場專案需要透過 aux 匯流排來路由幹信號，而在 `Project Acoustics Bus` 上攜帶濕信號。 下列處理常式支援對此信號流程進行漸進式遷移。

假設您有一個具有動作專案混音器階層的現有專案，其中包含*項措施師法*、*武器*及最上層的其他專案。 每個都有相對應的輸出匯流排來進行其試混搭。 假設您想要遷移項措施師法以使用聲場。 首先，建立對應的 aux 匯流排來攜帶 submix，這是項措施師法輸出匯流排的子系。 比方說，在下圖中，我們使用「晾乾」前置詞來組織匯流排，雖然確切名稱並不重要。 您在項措施師法匯流排上所擁有的任何計量或效果仍會如以往運作。

![建議的 Wwise 試混合設定](media/wwise-dry-mix-setup.png)

接下來，修改項措施師法動作專案-混音器的匯流排輸出結構，如下所示，並將*聲場專案匯流排*設定為**輸出匯流排**，並將*Dry_Footsteps*設定為使用者定義的 aux 匯流排。

![建議的 Wwise 動作專案混音器匯流排設定](media/actor-mixer-bus-settings.png)

現在，所有項措施師法都會收到聲場處理，並將其在聲場專案匯流排上的回音輸出。 試信號會如往常般透過 Dry_Footsteps 和 hrtf 路由傳送。

聲場專案只適用于世界上有3D 位置的音效。 遵循[Wwise 檔](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)，必須設定位置屬性，如下所示。 **3D Spatialization**設定可以是*位置*或*位置 + 方向*（視需要）。

![建議的 Wwise 動作專案定位設定](media/wwise-positioning.png)

您無法將**輸出匯流排**設定為將上游混合成*聲場專案匯流排*的某些其他匯流排。 Wwise 會在混音器外掛程式上強加這項需求。

如果您想要讓項措施師法動作專案混音器階層中的子系不使用聲場，您可以在其上使用「覆寫父系」來退出宣告。

如果您在遊戲中的任何動作專案混音器上使用遊戲定義或使用者定義的傳送來進行回音，請在該動作專案-混音器上將其關閉，以避免套用「回音」兩次。

#### <a name="spatialization"></a>Spatialization
聲場專案 Wwise 混音器外掛程式預設會套用卷加回回音，讓 Wwise 執行移動流覽 spatialization。 當您使用聲場專案在此預設僅限時僅限時設定中，您可以在您的幹混合上使用任何通道設定和 spatialization 方法。 因此，您可以使用聲場專案的回音來混合和比對幾乎任何空間定位器。 您的選項包括以[Ambisonics 為基礎的雙聲道 spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/)和[Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound)。
 
聲場專案包含支援以物件為基礎之高解析度 HRTF 轉譯和移動流覽功能的選擇性空間定位器。 選取混音器外掛程式設定中的 [**執行 Spatialization** ] 核取方塊，然後選擇 [ *HRTF* ] 或 [*移動*流覽]。 此外，請停用使用者定義的 aux 傳送至所有的幹匯流排，以避免聲場專案混音器外掛程式和 Wwise spatializing 兩次。 無法即時變更 spatialization 模式，因為它需要重新產生音效。 重新開機 Unreal，然後在選取 [播放] 來整合混音器外掛程式設定變更之前重新產生 soundbanks，例如 [**執行 Spatialization** ] 核取方塊設定。

![Wwise 混音器外掛程式 Spatialization 設定](media/mixer-spatial-settings.png)

可惜的是，其他以物件為基礎的空間定位器外掛程式目前不受支援。 它們會實作為混音器外掛程式，而 Wwise 不允許將多個混音器外掛程式指派給單一動作專案混音器。  

### <a name="audio-setup-in-unreal"></a>Unreal 中的音訊設定
1. 首先，您必須製作遊戲層級來產生聲場資產，這會放在*Content\Acoustics*中。 請參閱[Unreal 製作教學](unreal-baking.md)課程。 範例套件中包含一些預先內建的層級。

1. 在您的場景中建立聲場空間動作專案。 請只在層級建立其中一個執行者，因為它代表整個層級的聲場。

    ![在 Unreal 編輯器中建立聲場空間動作專案](media/create-acoustics-space.png)

1. 將內建聲場資料資產指派給聲場空間動作專案上的聲場資料插槽。 您的場景現在已經聲場了！

    ![Unreal 編輯器中的聲場資產指派](media/acoustics-asset-assign.png)

1. 加入空的執行者。 如下所示進行設定。

    ![[Unreal 編輯器] 會在空白執行者中顯示聲場元件使用方式](media/acoustics-component-usage.png)

       
    <sup>1</sup>將聲場音訊元件新增至動作專案。 此元件會將聲場專案功能新增至 Wwise 音訊元件。
        
    <sup>2</sup>預設會選取 [**在開始時播放] 方塊**。 此設定會在層級啟動時觸發相關聯的 Wwise 事件。</li>
         
    <sup>3</sup>使用 [**顯示**聲場參數] 核取方塊來列印有關來源的螢幕上的調試資訊。

    ![已啟用 debug 值的音效來源上的 Unreal 編輯器聲場面板](media/debug-values.png)

    <sup>4</sup>根據平常的 Wwise 工作流程指派 Wwise 事件。
       
    <sup>5</sup>請確定已關閉 [**使用空間音訊**]。 如果您使用特定音訊元件的聲場專案，就無法同時使用 Wwise 的空間音訊引擎來進行聲場。</li>
       
您全都準備好了。 四處移動場景並探索聲場效應！

## <a name="next-steps"></a>後續步驟
* 試用[聲場專案 Unreal/Wwise Design 教學](unreal-workflow.md)課程。
* 瞭解如何為您的遊戲場景[執行 bake](unreal-baking.md) 。
