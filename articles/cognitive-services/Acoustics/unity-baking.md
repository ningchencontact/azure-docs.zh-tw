---
title: 聲場專案 Unity 聲場模擬教學課程
titlesuffix: Azure Cognitive Services
description: 本教學課程描述在 Unity 中運用聲場專案進行聲場模擬。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: cb5ad8e4ff3d5a28fa38c7e8972e7e3e69d2762d
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136934"
---
# <a name="project-acoustics-unity-bake-tutorial"></a>聲場專案 Unity 聲場模擬教學課程
本教學課程描述在 Unity 中運用聲場專案進行聲場模擬。

軟體需求：
* 適用於 Windows 的 [Unity 2018.2+](http://unity3d.com)
* [Unity 專案中整合的聲場專案外掛程式](unity-integration.md)或[聲場專案 Unity 範例內容](unity-quickstart.md)
* 選用：[Azure Batch 帳戶](create-azure-account.md)，以使用雲端運算加快聲場模擬速度

## <a name="open-the-project-acoustics-bake-window"></a>開啟聲場專案聲場模擬視窗
從 Unity 功能表選擇 [Window] \(視窗\) > [Acoustics] \(聲場\) ：

![開啟 [Acoustics] \(聲場\) 視窗](media/window-acoustics.png)

## <a name="create-a-navigation-mesh"></a>建立導航網格
聲場專案會使用導航網格放置聆聽者探查點以進行模擬。 您可以使用 Unity 的[導航網格工作流程](https://docs.unity3d.com/Manual/nav-BuildingNavMesh.html)，或使用其他 3D 模型製作套件以設計自己的網格。 

## <a name="mark-acoustic-scene-objects"></a>標示聲場場景物件
聲場專案依賴兩種類型的場景物件進行模擬：模擬中將會反射及遮蔽聲音的物件，以及模擬中限制聆聽者探查點的播放機導航網格。 系統會使用 [Objects] \(物件\) 索引標籤標示這兩種物件類型。 

由於標記物件只會將 **AcousticsGeometry** 或 **AcousticsNavigation** 元件新增至物件，因此您也可以使用[標準 Unity 元素工作流程](https://docs.unity3d.com/Manual/UsingComponents.html)標示或取消標示物件。 可標示的物件僅限 Mesh Renderer (網格轉譯器) 和 Terrain (地形)。 所有其他物件類型都會被忽略。 核取方塊將會對所有受影響的物件進行標示或取消標示。

### <a name="mark-acoustic-occlusion-and-reflection-geometry"></a>標示聲場遮蔽和反射幾何
開啟 [Acoustics] \(聲場\) 視窗的 [Objects] \(物件\) 索引標籤。 如果物件應該遮蔽、反射或吸收聲音，請將它標示為 [Acoustics Geometry] (聲場幾何)。 聲場幾何可以包括地面、牆面、屋頂、窗戶和窗戶玻璃、地毯及大型家具。 對於這些物件，您可以使用任意層級的複雜度。 由於場景已在模擬之前進行體素化，因此具高度細節之網格 (例如包含許多小型葉片的樹木) 的聲場模擬成本並不會高於較簡單的物件。

請勿包括不應該影響聲場的物件，例如看不見的遮蔽網格。

製作結果中會修正進行探查計算 (透過下方的 [Probes] \(探查\) 索引標籤) 時的物件轉換。 如果移動場景中任何已標示的物件，將必須重做探查計算及重新製作場景。

### <a name="mark-the-navigation-mesh"></a>標示導航網格
聲場系統將會採用以 Unity 工作流程建立的導航網格。 若要使用您自己的網格，請從 [Objects] \(物件\) 索引標籤標示它們。

### <a name="for-reference-the-objects-tab-parts"></a>參考：[Objects] \(物件\) 索引標籤組件
索引標籤頁面的組件包括：

1. 索引標籤選取按鈕 (已選取 [Objects] \(物件\) 索引標籤)。 請使用這些按鈕來逐步完成進行聲場製作的各種步驟 (由左至右)。
2. 使用此頁面時所需執行之操作的簡短描述。
3. 階層視窗的可用篩選。 請使用此組件將階層視窗篩選成顯示所指定類型的物件，以便更容易標示這些物件。 如果您尚未針對聲場標示任何物件，則選取最後兩個選項將不會顯示任何內容。 不過，一旦您已標示物件，它們便可協助您找出已標示的物件。
4. 未選取任何物件時，此區段會顯示場景中所有物件的狀態：
    * Total (全部) - 場景中的作用中、非隱藏物件總數。
    * Ignored (已忽略) - 不是 Mesh Renderer (網格轉譯器) 或 Terrain (地形) 的物件數目。
    * Mesh (網格) - 場景中的 Mesh Renderer (網格轉譯器) 物件數目
    * Terrain (地形) - 場景中的 Terrain (地形) 物件數目
    * Geometry (幾何) - 場景中標示為 [Acoustics Geometry] \(聲場幾何\) 的 Mesh (網格) 或 Terrain (地形) 物件數目
    * Navigation (導航) - 場景中標示為 [Acoustics Navigation] \(聲場導航\) 的 Mesh (網格) 或 Terrain (地形) 物件數目 此數目不包含 Unity 的 NavMesh。
5. 顯示場景中「可標示的」物件 (僅限 Mesh Renderer (網格轉譯器) 和 Terrain (地形)) 總數。 顯示您可用來將這些物件標示為 (為其新增適當的元件) 用於聲場之幾何或導航的核取方塊
6. 未選取任何物件時，如有需要，此附註會提醒您選取物件來進行標示。 您也可以在不選取任何物件的情況下，選取一個或同時選取兩個核取方塊來標示場景中的所有物件。
7. 已選取物件時，此區段只會顯示所選物件的狀態。
8. 顯示「可標示的」所選物件總數。 選取或取消選取核取方塊只會對選取的物件進行標示或取消標示。

如果您的場景中沒有任何已選取的物件，[Objects] \(物件\) 索引標籤看起來就會類似下圖：

![未選取任何物件時的 [Objects] \(物件\) 索引標籤](media/objects-tab-no-selection-detail.png)

如果您的場景中有已選取的物件，則看起來會類似下圖：

![未選取任何物件時的 [Objects] \(物件\) 索引標籤](media/objects-tab-selection-detail.png)

如果有些物件已標示而有些未標示，則適當的核取方塊會顯示「混合」值：

![混合值核取方塊](media/mixed-object-selection-detail.png)

按一下此核取方塊會強制標示所有物件，再按一下則會將所有物件都取消標示。

您可以將物件標示成用於幾何和導航。

## <a name="select-acoustic-materials"></a>選取聲場材質
標示您的物件之後，請按一下 [Materials] \(材質\) 按鈕並指派聲場材質。 聲場專案材質系統會繫結至 Unity 視覺材質系統：具有個別聲場材質的兩個物件必須有個別的視覺材質。

聲場材質會控制從每個表面反射回來的聲音能量多寡。 預設聲場材質的吸收率與混凝土相當。 聲場專案會根據視覺材質名稱建議材質。 您可以將聲場材質「自訂」指派給材質，以啟用吸收率係數滑桿。

室內所指定材質的殘響時間與其吸收係數成反比，其中大多數材質的吸收值在 0.01 到 0.20 的範圍。 材質的吸收係數若是在此範圍外，則極具吸收性。

![殘響時間圖](media/reverb-time-graph.png)

### <a name="for-reference-parts-of-the-materials-tab"></a>參考：[Materials] (材質) 索引標籤的組件
![[材質] 索引標籤詳細資料](media/materials-tab-detail.png)

1. 用來顯示此頁面的 [Materials] \(材質\) 索引標籤按鈕，。
2. 使用此頁面時所需執行之操作的簡短描述。
3. 已選取時，只會列出標示為 [Acoustics Geometry] \(聲場幾何\) 之物件所使用的材質。 否則，會列出場景中所使用的所有材質。
4. 使用這些選項來變更按一下下方 [Acoustics] \(聲場\) 欄 (第 6 點) 中的下拉式清單時，所顯示下拉式功能表的順序。 [Name] \(名稱\) 會將聲場材質依名稱排序。 [Absorptivity] \(吸收性\) 則會將它們依吸收性由低至高排序。
5. 場景中所使用的材質清單，依字母順序排序。 如果選取 [Show Marked Only] \(只顯示已標示的物件\) (第 3 點)，便只會列出標示為 [Acoustics Geometry] \(聲場幾何\) 之物件所使用的材質。 按一下這裡的某個材質，就會選取場景中使用該材質的所有物件。
6. 顯示作為場景材質指派對象的聲場材質。 按一下下拉式清單，即可將場景材質重新指派給不同的聲場材質。 您可以使用上面的 [Sort Acoustics By] \(聲場排序依據\) 選項 (第 4 點)，來變更按一下此處項目時所顯示功能表的排序順序。
7. 顯示在上一欄中所選材質的聲場吸收係數。 值為零時表示完全反射 (沒有任何吸收)，值為 1 時則表示完全吸收 (沒有任何反射)。 除非所選材質為 [Custom] \(自訂\)，否則無法變更吸收係數。
8. 針對指派給 [Custom] \(自訂\) 的材質，滑桿便不再停用，您可以使用滑桿或透過輸入值來選擇吸收係數。

## <a name="calculate-and-review-listener-probe-locations"></a>計算並檢閱聆聽者探查位置
指派材質之後，請切換為 [Probes] \(探查\) 索引標籤，然後按一下 [Calculate] \(計算\) 以放置聆聽者探查點進行模擬。

### <a name="what-the-calculate-button-calculates"></a>[Calculate] \(計算\) 按鈕會計算什麼
[Calculate] \(計算\) 按鈕會使用您選取的聲場場景幾何來準備模擬場景：

1. 它會從場景網格中取用幾何，然後計算出體素體積。 體素體積是一個圍繞您整個場景的 3D 體積，由小型的立方體「體素」所組成。 體素的大小取決於 [Simulation Resolution] \(模擬解析度\) 設定所設定的模擬頻率。 每個體素都會標示為 [open air] \(室外\) 或包含場景幾何。 如果體素包含幾何，體素上就會標示指派給該幾何之材質的吸收係數。
2. 它會使用導航網格放置聆聽者探查點。 演算法會平衡空間覆蓋、模擬時間與檔案大小的競爭考量，同時會確保狹窄的走廊和小空間始終具有一定大小的涵蓋範圍。 典型的探查點計算範圍是從 100 (小型場景) 或至數千 (大型場景)。

視您場景的大小和機器的運算速度而定，這些計算可能會花費數分鐘的時間。

### <a name="review-voxel-and-probe-placement"></a>檢閱體素和探查位置
檢閱體素資料和探查點位置，以確保您能夠準備好為場景進行聲場模擬。 不完整的導航網格或遺漏/有多餘聲場幾何等情況，通常都能在預覽中迅速發現。 您可以使用 Gizmos 功能表啟用或停用體素與探查位置：

![Gizmos 功能表](media/gizmos-menu.png)

包含聲場幾何的體素會顯示為綠色立方體。 請探索您的場景並確認應該是幾何的每個項目都有體素。 場景相機必須位於物件的大約 5 公尺以內，才能顯示體素。

如果您比較以 [Corse] (粗略) 解析度和 [Fine] (精細) 解析度建立的體素，將會發現粗略體素的體積是兩倍大。

![體素預覽](media/voxel-cubes-preview.png)

模擬結果會在執行階段插入至聆聽者探查點位置之間。 請檢查您預期玩家會於場景中行經的所有地方附近是否都有探查點。

![探查預覽](media/probes-preview.png)

### <a name="take-care-with-scene-renames"></a>重新命名場景時請謹慎
場景名稱可用來將場景連接至儲存探查點位置和體素化設定的檔案。 如果在計算出探查點之後將場景重新命名，就會遺失材質指派和位置資料，而應該重新執行。

### <a name="for-reference-parts-of-the-probes-tab"></a>參考：[Probes] \(探查\) 索引標籤的組件
![[Probes] \(探查\) 索引標籤詳細資料](media/probes-tab-detail.png)

1. 用來顯示此頁面的 [Probes] \(探查\) 索引標籤按鈕
2. 使用此頁面時所需執行之操作的簡短描述
3. 使用這些來選擇粗略或精細模擬解析度。 [Coarse] \(粗略\) 的執行速度較快，但會有所取捨。 如需詳細資料，請參閱下面的[選擇粗略或精細解析度](#Coarse-vs-Fine-Resolution)。
4. 使用此欄位來選擇聲場資料檔的預期放置位置。 按一下具有 "..." 的按鈕以使用資料夾選擇器。 預設值為 **Assets/AcousticsData**。 此外，也會在此位置底下建立一個 [Editor] 子資料夾。 如需有關資料檔的詳細資訊，請參閱下面的[資料檔](#Data-Files)。
5. 此場景的資料檔將會採用這裡提供的前置詞來命名。 預設值為 "Acoustics_[Scene Name]"。
6. 計算探查之後，就會停用上述控制項。 按一下 [Clear] \(清除\) 按鈕以清除計算並啟用控制項，以便讓您能夠使用新的設定來重新計算。
7. 按一下 [Calculate] \(計算\) 按鈕以將場景體素化 (Voxelize) 及計算探查點位置。 這會在您電腦本機完成，且必須在進行製作之前完成。

在這個版本的 Project Acoustics 中，無法手動放置探查，而必須透過 [Probes] \(探查\) 索引標籤中提供的自動化流程放置。

### <a name="Coarse-vs-Fine-Resolution"></a>選擇粗略或精細解析度

[Coarse] \(粗略\) 與 [Fine] \(精細\) 解析度設定之間的唯一差異在於模擬執行頻率。 [Fine] (精細) 所使用的頻率為 [Coarse] (粗略) 的兩倍。
雖然這看似簡單，但在聲場模擬上具有一些含意：

* [Coarse] (粗略) 的波長為 [Fine] (精細) 的兩倍，因此體素是兩倍大。
* 由於聲場模擬時間與體素大小直接相關，因此 [Coarse] (粗略) 模擬比 [Fine] (精細) 模擬快 16 倍。
* 針對小於體素大小的門戶 (例如門或窗) 將無法進行模擬。 [Coarse] (粗略) 設定可能導致沒有模擬部分較小的門戶，因此它們將不會在執行階傳遞聲音。 您可以檢視體素來查看是否會發生這種情況。
* 模擬頻率越低，在角落和邊緣周圍產生的繞射就越少。
* 聲音來源不能位於「已填滿的」體素 (也就是包含幾何的體素) 內，這會導致沒有聲音。 這會更難找出聲音來源，因此它們不會在與使用「精細」設定相比更大的「粗略」設定體素內。
* 較大的體素侵入門戶的部分也較大，如下所示。 第一張圖是使用 [Coarse] (粗略) 解析度來建立的，第二張圖是相同的門口，但使用 [Fine] (精細) 解析度。 如紅色標記所示，使用 [Fine] (精細) 設定時，侵入門口的程度較小。 藍線是幾何所定義的門口，紅線則是體素大小所定義的有效聲場門戶。 此入侵狀況在指定的情況下會如何表現，完全取決於體素與門戶幾何的對齊方式，而這又取決於場景中物件的大小和位置。

![粗略門口](media/coarse-voxel-doorway.png)

![精細門口](media/fine-voxel-doorway.png)

## <a name="bake-your-scene-using-azure-batch"></a>使用 Azure Batch 來對場景進行聲場模擬
您可以使用 Azure Batch 服務搭配雲端中的計算叢集來對場景進行聲場模擬。 聲場專案 Unity 外掛程式會直接連線至 Azure Batch，以針對每個聲場模擬具現化、管理及卸除 Azure Batch 叢集。 在 [Bake] (聲場模擬) 索引標籤中，選取叢集機器類型和大小，然後按一下 [Bake] (模擬聲場)。

### <a name="for-reference-parts-of-the-bake-tab"></a>參考：[Bake] (聲場模擬) 索引標籤的組件
![[Bake] \(製作\) 索引標籤詳細資料](media/bake-tab-details.png)

1. 用來顯示此頁面的 [Bake] \(製作\) 索引標籤按鈕。
2. 要在此頁面上執行之操作的簡短描述。
3. 建立您的 Azure 帳戶之後，用來輸入 Azure 認證的欄位。 如需詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
4. 適用於聲場工具組的 Docker 影像標籤。
5. 啟動 Azure 入口網站來管理您的訂用帳戶、監視使用量和檢視帳單資訊等。 
6. 要用於計算的 Azure Batch 計算節點類型。 此節點類型必須是您 Azure 資料中心位置支援的類型。 如果不確定，請保持為 **Standard_F8s_v2**。
7. 要用於此計算的節點數目。 您在這裡輸入的數目會影響完成製作的時間，並且會受到 Azure Batch 核心配置限制。 預設配置只允許兩個 8 核心節點或一個 16 核心節點，但可以擴充。 如需有關核心配置條件約束的詳細資訊，請參閱[建立 Azure Batch 帳戶](create-azure-account.md)。
8. 選取此核取方塊，以將計算集區設定為使用[低優先順序節點](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)。 低優先順序的計算節點具有更低的成本，但它們可能無法提供使用，或可能隨時會被搶佔。
9. 您場景的探測計數，如 [Probes] \(探測\) 索引標籤上所計算。探測數目會決定需要在雲端執行的模擬次數。 您指定的節點數目不可超過探測數目。
10. 您的作業在雲端執行時預期將耗用的時間長度。 這不包括節點啟動時間。 作業開始執行之後，這大約會等於取得結果所需的時間。 請注意，這只是一個預估值。
11. 執行模擬所需的運算時間量總計。 這是將在 Azure 中使用的節點計算時間量總計。 如需有關使用此值的詳細資訊，請參閱下面的[預估製作成本](#Estimating-bake-cost)。
12. 此訊息會告訴您在作業結束之後會將製作結果儲存在哪裡。
13. (僅限進階使用) 如果您因某個原因而需要強制 Unity 忘記您已提交的製作項目 (例如您已使用另一部機器來下載結果)，請按一下 [Clear State] \(清除狀態\) 按鈕來忘記已提交的作業。 請注意，這意謂著在備妥結果檔案時，將**不會**下載結果檔案，**這與取消作業並不相同**。 作業如果正在執行，將會繼續在雲端執行。
14. 按一下 [Bake] \(製作\) 按鈕以將聲場模擬項目提交至雲端。 當作業正在執行時，這會改為顯示 [Cancel Job] \(取消作業\)。
15. 準備[在電腦上處理聲場模擬](#Local-bake)。
16. 此區域會顯示製作狀態。 完成時，應該會顯示 [Downloaded] \(已下載\)。

您一律可以在 [Azure 入口網站](https://portal.azure.com)取得有關作用中作業、計算集區及儲存體的完整資訊。

當作業正在執行時，[Bake] \(製作\) 按鈕會變更為 [Cancel Job] \(取消作業\)。 使用此按鈕即可取消進行中的作業。 刪除工作之前，系統會先要求您確認。 取消作業後即無法復原、將無法取得任何結果，且您仍然需支付任何已使用的 Azure 計算時間費用。

在您開始製作之後，即可關閉 Unity。 視專案、節點類型與節點數目而定，雲端製作可能會花費數小時的時間。 當您重新載入專案並開啟 [Acoustics] \(聲場\) 視窗時，製作作業狀態將會更新。 如果作業已完成，系統將會下載輸出檔。

Azure 認證會安全地儲存在您的本機電腦上，並與您的 Unity 編輯器建立關聯。 它們僅供用來與 Azure 建立安全連線。

### <a name="Estimating-bake-cost"></a> 正在評估 Azure 製作成本

若要預估所指定製作項目的成本，請取用在 [Estimated Compute Cost] \(預估計算成本\) 顯示的值 (這是一個持續時間)，然後將其乘以您所選 [VM Node Type] \(VM 節點類型\) 的每小時成本 (採用您的當地貨幣)。 此結果將不會包含讓節點啟動並開始執行所需的節點時間。 例如，如果您選取 [Standard_F8s_v2] 作為您的節點類型，其成本為每小時 0.40 美元，而 [Estimated Compute Cost] \(預估計算成本\) 為 3 小時又 57 分鐘，則執行作業的預估成本將會是 0.40 美元 * ~4 小時 = ~1.60 美元。 實際成本可能會稍微高一點，因為讓節點啟動需要額外的時間。 您可以在 [Azure Batch 定價](https://azure.microsoft.com/pricing/details/virtual-machines/linux) 頁面上找到每小時節點成本 (針對類別選取 [計算最佳化] 或 [高效能計算])。

## <a name="Local-bake"></a> 在電腦上針對場景進行聲場模擬
您可以在電腦上針對場景進行聲場模擬。 這很適合用於在建立 Azure Batch 帳戶之前，先行運用小型場景試驗聲場。 請注意，根據場景的大小而定，聲場模擬可能需要很長的時間。

### <a name="minimum-hardware-requirements"></a>最低硬體需求
* 具備至少 8 個核心和 32 GB RAM 的 x86-64 處理器

例如，在配備 Intel Xeon E5-1660 @ 3 GHz 和 32 GB RAM 的 8 核心電腦上測試 -
* 具有 100 個探查的小型場景，大約需要 2 小時進行粗略聲場模擬，或需要 32 小時進行精細聲場模擬。
* 具有 1000 個探查的小型場景，大約需要 20 小時進行粗略聲場模擬，或需要 21 天進行精細聲場模擬。

### <a name="setup-docker"></a>設定 Docker
在將會處理模擬的電腦上安裝和設定 Docker -
1. 安裝 [Docker 工具組](https://www.docker.com/products/docker-desktop)。
2. 啟動 Docker 設定、瀏覽至 [進階] 選項，並設定至少具備 8 GB RAM的資源。 您可以配置給 Docker 的 CPU 越多，製作完成的速度就越快。 ![Docker 設定範例](media/docker-settings.png)
3. 瀏覽至 [共用磁碟機]，然後開啟用於處理之磁碟機的共用。![DockerDriveSharing](media/docker-shared-drives.png)

### <a name="run-local-bake"></a>執行本機製作
1. 按一下 [Bake] \(聲場模擬\) 索引標籤上的 [Prepare Local Bake] \(準備在本機進行聲場模擬\) 按鈕，然後選取要儲存輸入檔案和執行指令碼的資料夾。 然後，您可以在任何電腦上執行製作，只要它符合最低硬體需求，並藉由將資料夾複製到該電腦來安裝 Docker。
2. 使用 "runlocalbake.bat" 指令碼啟動模擬。 該指令碼將使用模擬處理所需的工具組擷取 Project Acoustics Docker 映像，並啟動模擬。 
3. 模擬完成後，請將產生的 .ace 檔案複製回您的 Unity 專案。 若要確定 Unity 會將此辨識為二進位檔案，請將 ".bytes" 附加到檔案副檔名 (例如，"Scene1.ace.bytes")。 模擬的詳細記錄檔會儲存在 "AcousticsLog.txt" 中。 如果您遇到任何問題，請共用此檔案以協助進行診斷。

## <a name="Data-Files"></a> 由聲場模擬程序新增的資料檔案

聲場模擬程序進行期間會建立四個資料檔。 其中包含模擬結果並隨附於您的標題。 其他存放區 Unity 編輯器相關資料。

模擬結果：
* **Assets/AcousticsData/Acoustics\_[SceneName].ace.bytes**：這是執行階段查閱資料表，而且包含模擬結果和已體素化的聲場場景元素。 您可以使用 [Probes] \(探查\) 索引標籤上的欄位，來變更此檔案的位置和名稱。

請注意不要刪除模擬結果檔案。 除非針對場景重新進行聲場模擬，否則無法復原此檔案。

編輯器資料檔：
* **Assets/Editor/[SceneName]\_AcousticsParameters.asset**：這個檔案會儲存您在聲場 UI 欄位中輸入的資料。 您無法變更此檔案的位置和名稱。
* **Assets/AcousticsData/Editor/Acoustics_[SceneName].vox**：這個檔案會儲存使用 [Probes] \(探查\) 索引標籤中 [Calculate] \(計算\) 按鈕計算的體素化聲場幾何和材質屬性。您可以使用 [Probes] \(探查\) 索引標籤上的欄位，來變更此檔案的位置和名稱。
* **Assets/AcousticsData/Editor/Acoustics\_[SceneName]\_config.xml**：這個檔案會儲存使用 [Probes] \(探查\) 索引標籤上的 [Calculate] \(計算\) 按鈕計算的模擬參數。您可以使用 [Probes] \(探查\) 索引標籤上的欄位，來變更此檔案的位置和名稱。

## <a name="set-up-the-acoustics-lookup-table"></a>設定聲場查閱資料表
將 [Project Acoustics] 預製項目從專案面板拖放到您的場景中：

![聲場預製項目](media/acoustics-prefab.png)

按一下 [ProjectAcoustics] 遊戲物件以前往其偵測器面板。 透過將製作結果 (**Assets/AcousticsData** 中的 .ACE 檔案) 拖放到 [Acoustics Manager] \(聲場管理員\) 指令碼中，或按一下文字方塊旁邊的圓形按鈕，來指定製作結果的位置。

![聲場管理員](media/acoustics-manager.png)  

## <a name="next-steps"></a>後續步驟
* 瀏覽 [Unreal 的設計控制項](unity-workflow.md)
* 瀏覽[聲場專案設計概念](design-process.md)

