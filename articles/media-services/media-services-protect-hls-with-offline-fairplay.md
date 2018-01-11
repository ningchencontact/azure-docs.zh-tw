---
title: "使用離線 Apple FairPlay 保護 HLS 內容 - Azure | Microsoft Docs"
description: "本主題提供概觀，並示範如何使用 Azure 媒體服務，利用離線模式的 Apple FairPlay 動態加密您的 HTTP 即時串流 (HLS) 內容。"
services: media-services
keywords: "HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10, FairPlay 串流 (FPS), 離線"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 15f6d422f3171ae5161e0d4d4bcd8ec98529c766
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="offline-fairplay-streaming"></a>離線 FairPlay 串流
 Azure Media Services 提供一組完善[內容保護服務](https://azure.microsoft.com/services/media-services/content-protection/)該封面：

- Microsoft PlayReady (英文)
- Google Widevine
- Apple FairPlay
- AES-128 加密

數位版權管理 (DRM) / 要求不同的資料流通訊協定的動態執行進階加密標準 (AES) 加密的內容。 DRM 授權/AES 解密金鑰傳遞服務也會提供媒體服務。

除了透過各種不同的串流處理通訊協定來保護內容進行線上串流處理，受保護內容的離線模式也是一個經常被要求的功能。 下列案例需要離線模式支援：

* 旅行時無法使用，例如網際網路連線時播放。
* 某些內容提供者可能不允許 DRM 授權傳遞超出國家/地區的框線。 如果使用者想要觀賞的國家/地區外旅行時的內容，則需要離線的下載。
* 在某些國家/地區，網際網路可用性和/或頻寬則仍會限制。 使用者可能會選擇第一次下載到能夠觀賞內容夠高，令人滿意的檢視方式的解析度。 在此情況下，問題通常不網路可用性而有限的網路頻寬。 透過--top (OTT) / 線上視訊平台 (OVP) 提供者要求離線模式支援。

本文涵蓋 FairPlay 串流 (FPS) 離線模式支援執行 10 或更新版本的 iOS 裝置為目標。 為其他 Apple 平台，例如 watchOS、 tvOS 或 Safari macOS 上不支援此功能。

## <a name="preliminary-steps"></a>預備步驟
在您實作離線 DRM FairPlay 的 iOS 10 + 裝置上：

* 熟悉適用於 FairPlay 的線上內容保護。 如需詳細資訊，請參閱下列文章和範例：

    - [Azure 媒體服務資料流的 Apple FairPlay 是正式推出](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [使用 Apple FairPlay 或 Microsoft PlayReady 保護 HLS 內容](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [線上 FPS 串流的範例](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/) \(英文\)

* 從 Apple 開發人員網路取得 FPS SDK。 FPS SDK 包含兩個元件：

    - FPS Server SDK，包含金鑰安全性模組 (KSM)、 用戶端範例、 規格和一組測試向量。
    - FPS 部署套件，其中包含 D 函式的規格，以及有關如何產生 FPS 憑證、 客戶專屬私用金鑰和應用程式密碼金鑰的指示。 Apple 發給 FPS 部署組件僅授權的內容提供者。

## <a name="configuration-in-media-services"></a>在 Media Services 的組態
透過 FPS 離線模式組態[Media Services.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)、 使用 Media Services.NET SDK 版本 4.0.0.4 或更新版本中，以提供必要的 API，來設定 FPS 離線模式。
您也必須設定連線模式 FPS 內容保護運作的程式碼。 取得程式碼來設定連線模式下的 FPS 的內容保護之後，您需要下列兩個變更。

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay 組態中的程式碼變更
定義 「 啟用離線模式 」 是第一次變更布林值，呼叫 objDRMSettings.EnableOfflineMode，它可讓離線的 DRM 案例時，則為 true。 根據此指標，請對 FairPlay 設定進行下列變更：

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>資產傳遞原則設定中的程式碼變更
第二項變更是加入到字典 < AssetDeliveryPolicyConfigurationKey 字串 > 的第三個索引鍵。
加入 AssetDeliveryPolicyConfigurationKey，如下所示：
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

這個步驟之後，FPS 資產傳遞原則中的 < Dictionary_AssetDeliveryPolicyConfigurationKey > 字串，包含下列三個項目：

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 或 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl，取決於一些因素，例如使用的 FPS KSM/金鑰伺服器和重複使用相同的資產傳遞是否跨多個資產的原則
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

現在您的 Media Services 帳戶設定為傳遞離線 FairPlay 授權。

## <a name="sample-ios-player"></a>範例 iOS 播放程式
僅適用於 iOS 10 和更新版本 FPS 離線模式支援。 FPS Server SDK （版本 3.0 或更新版本） 包含的文件和 FPS 離線模式的範例。 具體來說，FPS Server SDK （版本 3.0 或更新版本） 包含下列兩個項目相關以離線模式：

* 文件: 「 FairPlay 串流的離線播放和 HTTP 即時資料流。 」 Apple，2016 年 9 月 14 日。 在 FPS 伺服器 SDK 4.0 版，這份文件會合併到主要的 FPS 文件。
* 範例程式碼： FPS 離線模式中 \FairPlay Streaming 伺服器 SDK 版本 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ HLSCatalog 範例。 HLSCatalog 範例應用程式，下列程式碼檔會用來實作離線模式功能：

    - AssetPersistenceManager.swift 程式碼檔案： AssetPersistenceManager 是在此範例中示範的主要類別如何：

        - 管理下載的 HLS 資料流，例如用來啟動和取消下載，以及刪除現有的資產，關閉裝置的 Api。
        - 監視的下載進度。
    - AssetListTableViewController.swift 和 AssetListTableViewCell.swift 程式碼檔案：AssetListTableViewController 是此範例的主要介面。 它提供範例可用於播放、 下載、 刪除或取消下載的資產的清單。 

這些步驟顯示如何設定執行的 iOS media player。 假設您從 HLSCatalog 範例 FPS Server SDK 版本 4.0.1 中啟動，變更下列程式碼：

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift，實作方法`requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)`使用下列程式碼。 可讓 「 drmUr 」 是指派給 HLS URL 的變數。

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，實作方法 `requestApplicationCertificate()`。 此實作取決於您會將憑證內嵌於裝置上 (僅限公開金鑰)，還是將憑證裝載於 Web 上。 下列的實作會使用這些測試的範例中使用的裝載應用程式憑證。 可讓 「 certUrl"是變數，其中包含應用程式憑證的 URL。

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

最後的整合測試的視訊的 URL 和應用程式憑證 URL 節提供了 「 整合式的測試 」。

在 HLSCatalog\Shared\Resources\Streams.plist，加入您測試的視訊 URL。 內容金鑰識別碼，請使用 skd 通訊協定中的 FairPlay 授權取得 URL，做為唯一的值。

![離線 FairPlay iOS 應用程式串流](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

使用您自己測試視訊 URL、 FairPlay 授權取得 URL 和應用程式的憑證 URL，如果您有設定的話。 或者，您可以繼續下一節，其中包含測試範例。

## <a name="integrated-test"></a>整合式的測試
在 Media Services 的三個測試範例涵蓋下列三種案例：

* 受到視訊、 音訊及其他音訊播放軌的 FPS
* 受保護，具有視訊和音訊，但沒有替代的音訊播放軌的 FPS
* 每秒畫面格的任何音訊和視訊只受到保護

您可以找到這些範例[這個示範網站](http://aka.ms/poc#22)，與對應的應用程式憑證中，裝載於 Azure web 應用程式。
與第 3 版或第 4 版的 FPS Server SDK 的範例，如果主要的播放清單包含替代的音訊，在離線模式期間播放音訊只。 因此，您必須刪除替代的音訊。 換句話說，所列出的第二個和第三個範例先前在線上及離線模式中運作。 列出此範例第一次會播放音訊只在離線模式中，線上串流正常運作時。

## <a name="faq"></a>常見問題集
以下常見問題集提供疑難排解的協助：

- **為什麼沒有唯一的音訊播放但不是視訊期間離線模式？** 這個行為似乎是範例應用程式的設計使然。 替代的音訊播放軌時 （這是對 HLS 的案例） 出現在離線模式下，iOS 10 和 iOS 11 預設會將其他音訊播放軌。為了彌補這種行為 FPS 離線模式，請從資料流移除替代的音訊播放軌。 若要 Media Services 上執行此作業，加入動態資訊清單的篩選器"碴錼 = false。 」 換句話說，HLS URL 結尾.ism/manifest(format=m3u8-aapl,audio-only=false)。 
- **為什麼沒有它仍播放音訊只是視訊不在離線模式期間之後加入碴錼 = false？** 根據內容傳遞網路 (CDN) 快取索引鍵設計，可能會快取內容。 清除快取。
- **除了 iOS 10，iOS 11 也支援 FPS 離線模式嗎？** 可以。 適用於 iOS 10 和 iOS 11 支援 FPS 離線模式。
- **為什麼 FPS Server SDK 中找不到 「 離線播放與 FairPlay 串流和 HTTP 即時資料流 」 的文件？** 第 4 版 FPS Server SDK，因為這份文件已合併到"FairPlay Streaming 程式設計指南。"
- **在以下適用於 FPS 離線模式的 API 中，最後一個參數代表什麼？**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    如需此 API 文件，請參閱[FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration 方法](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)。 此參數代表離線以租用方式佔用，以小時為單位的持續時間。
- **在 iOS 裝置上的下載/離線檔案結構為何？** IOS 裝置上下載的檔案結構看起來像下列螢幕擷取畫面。 `_keys`資料夾儲存區和每個授權服務主機的一個存放區檔案一起下載 FPS 授權。 `.movpkg`資料夾儲存音訊及視訊內容。 名稱結尾為虛線，後面接著數字的第一個資料夾包含視訊內容。 數值為的影片轉譯 PeakBandwidth。 第二個資料夾的名稱，後接 0 虛線結尾包含音訊內容。 第三個名為 "Data" 的資料夾包含 FPS 內容的主要播放清單。 最後，boot.xml 提供的完整描述`.movpkg`資料夾內容。 

![離線 FairPlay iOS 範例應用程式檔案結構](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

範例 boot.xml 檔案：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>總結
本文件包含下列步驟和資訊可用來實作 FPS 離線模式：

* 透過 Media Services.NET API 的媒體服務內容保護設定會設定在 Media Services 動態 FairPlay 加密和 FairPlay 授權傳遞。
* 從 FPS Server SDK 範例為基礎的 iOS media player 設定 iOS media player 可以播放 FPS 內容在線上的資料流處理模式或離線模式。
* 若要測試離線模式和線上串流可用範例 FPS 視訊。
* 常見問題集解答 FPS 離線模式的相關的問題。
