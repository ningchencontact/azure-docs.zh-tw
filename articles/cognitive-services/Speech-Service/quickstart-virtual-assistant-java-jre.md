---
title: 快速入門：自訂語音優先虛擬助理 (預覽)，Java (Windows, Linux) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何在 Java 主控台應用程式中使用認知服務語音軟體開發套件 (SDK)。 您將了解如何將用戶端應用程式連線到先前建立的 Bot Framework Bot，該 Bot 設定為使用 Direct Line Speech 頻道和啟用語音優先虛擬助理體驗。
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026628"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>快速入門：使用語音 SDK、Java 建立語音優先虛擬助理

在本文中，您會使用[認知服務語音 SDK](speech-sdk.md)，建立 Java 主控台應用程式。 此應用程式會連線到先前撰寫的 Bot，該 Bot 設定為使用 Direct Line Speech 頻道、傳送語音要求，以及傳回語音回應活動 (若已設定的話)。 此應用程式是使用語音 SDK Maven 套件，以及Windows、Ubuntu Linux 或 macOS 上的 Eclipse Java IDE 來建置。 它會在 64 位元 Java 8 Runtime Environment (JRE) 上執行。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* 作業系統：Windows (64 位元)、Ubuntu Linux 16.04/18.04 (64 位元) 或 macOS 10.13 或更新版本
* [Eclipse Java IDE](https://www.eclipse.org/downloads/) \(英文\)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) \(英文\) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) \(英文\)
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)。
* 預先設定的 Bot，其使用 Bot Framework 4.2 版或更新版本來建立。 Bot 必須訂閱新的 "Direct Line Speech" 頻道才能接收語音輸入。

    > [!NOTE]
    > 在預覽中，Direct Line 語音通道目前僅支援 **westus2** 區域。

    > [!NOTE]
    > [免費試用語音服務](get-started.md)中所述標準定價層的 30 天試用受限於 **westus** (不是 **westus2**)，因此與 Direct Line Speech 不相容。 免費與標準層 **westus2** 訂用帳戶相容。

如果您執行 Ubuntu 16.04/18.04，請先確定已安裝下列相依性，再啟動 Eclipse：

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

如果您執行 Windows (64 位元)，請確定您已為平台安裝 Microsoft Visual C++ 可轉散發套件：
* [下載適用於 Visual Studio 2017 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>選用：快速開始使用

本快速入門會逐步說明如何製作簡單的用戶端應用程式以連線到您具備語音功能的 Bot。 如果您想要深入了解，可以在[語音 SDK 範例](https://aka.ms/csspeech/samples)的 `quickstart` 資料夾之下取得本快速入門中使用的完整、可立即編譯的原始程式碼。

## <a name="create-and-configure-project"></a>建立及設定專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

此外，若要啟用記錄功能，請更新 **pom.xml** 檔案以包含下列相依性。

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>新增範例程式碼

1. 若要將新的空白類別新增到您的 Java 專案，請選取 [檔案] > [新增] > [類別]。

1. 在 [新增 Java 類別] 視窗中，於 [套件] 欄位中輸入 **speechsdk.quickstart**，並在 [名稱] 欄位中輸入 **Main**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](media/sdk/qs-java-jre-06-create-main-java.png)

1. 開啟新建立的 **Main** 類別，並以下列起始程式碼取代 `Main.java` 檔案的內容。

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. 在 **main** 方法中，您會先設定 `BotConnectorConfig` 並使用它來建立 `SpeechBotConnector` 執行個體。 這會連線至 Direct Line Speech 頻道來與您的 Bot 互動。 `AudioConfig` 執行個體也用來指定音訊輸入來源。 在此範例中，預設麥克風會搭配 `AudioConfig.fromDefaultMicrophoneInput()` 使用。

    * 將字串 `YourSubscriptionKey` 取代為您的訂用帳戶金鑰，您可以從[這裡](get-started.md)取得該金鑰。
    * 將字串 `YourServiceRegion` 取代為與您的訂用帳戶相關聯的[區域](regions.md)。
    * 將字串 `YourChannelSecret` 取代為您的 Direct Line Speech 頻道祕密。

    > [!NOTE]
    > 在預覽中，Direct Line Speech 頻道目前僅支援 **westus2** 區域。

    > [!NOTE]
    > [免費試用語音服務](get-started.md)中所述標準定價層的 30 天試用受限於 **westus** (不是 **westus2**)，因此與 Direct Line Speech 不相容。 免費與標準層 **westus2** 訂用帳戶相容。

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` 依賴多個事件來傳達其 Bot 活動、語音辨識結果及其他資訊。 接下來新增這些事件接聽程式。

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. 叫用 `connectAsync()` 方法，以將 `SpeechBotConnector` 連線到 Direct Line Speech。 若要測試您的 Bot，您可以叫用 `listenOnceAsync` 方法，以傳送來自麥克風的音訊輸入。 此外，您也可以使用 `sendActivityAsync` 方法，以序列化字串的形式傳送自訂活動。 這些自訂活動可以提供您的 Bot 會在交談中使用的其他資料。

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. 將變更儲存到 `Main` 檔案。

1. 為了支援回應播放，您會新增一個額外類別，其中包含可支援音訊的公用程式方法。 若要啟用音訊，請將另一個空的新類別新增至 Java 專案：選取 [檔案] > [新增] > [類別]。

1. 在 [新增 Java 類別] 視窗中，於 [套件] 欄位中輸入 **speechsdk.quickstart**，並在 [名稱] 欄位中輸入 **AudioPlayer**。

   ![[新增 Java 類別] 視窗的螢幕擷取畫面](media/sdk/qs-java-jre-06-create-main-java.png)

1. 開啟新建立的 **AudioPlayer** 類別，並以下面提供的程式碼取代。

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. 將變更儲存到 `AudioPlayer` 檔案。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

按 F11 鍵，或選取 [執行] > [偵錯]。
主控台會顯示「說一些話」訊息。此時，您可以說您的 Bot 可理解的英文片語或句子。 您的語音將透過 Direct Line Speech 頻道傳輸到您的 Bot，您的 Bot 將會辨識、處理語音，並以活動形式傳回回應。 如果您的 Bot 傳回語音作為回應，則會使用 `AudioPlayer` 類別播放音訊。

![成功辨識後主控台輸出的螢幕擷取畫面](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>後續步驟

在 GitHub 上可取得其他範例，例如如何讀取音訊檔案中的語音。

> [!div class="nextstepaction"]
> [瀏覽 GitHub 上的 Java 範例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另請參閱

- [快速入門：翻譯語音，Java (Windows、Linux)](quickstart-translate-speech-java-jre.md)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
