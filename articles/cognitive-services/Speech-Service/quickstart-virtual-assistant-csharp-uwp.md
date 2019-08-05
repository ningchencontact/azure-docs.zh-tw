---
title: 快速入門：自訂語音優先虛擬助理 (預覽)，C# (UWP) - 語音服務
titleSuffix: Azure Cognitive Services
description: 在本文中，您會使用認知服務語音軟體開發套件 (SDK) 來建立 C# 通用 Windows 平台 (UWP) 應用程式。 您會將用戶端應用程式連線到先前建立的 Bot Framework Bot，該 Bot 設定為使用 Direct Line Speech 頻道。 應用程式是使用語音 SDK NuGet 套件與 Microsoft Visual Studio 2017 所建置的。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: 6d88bcc6d9d2c2e46ef3a3cb841c9268a544e37c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609697"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>快速入門：使用語音 SDK、UWP 建立語音優先虛擬助理

另備有[語音轉文字](quickstart-csharp-uwp.md)、[文字轉語音](quickstart-text-to-speech-csharp-uwp.md)和[語音翻譯](quickstart-translate-speech-uwp.md)的快速入門。

在本文中，您會使用[語音 SDK](speech-sdk.md)來開發 C# 通用 Windows 平台 (UWP) 應用程式。 此程式會連線到先前撰寫和設定的 Bot，讓您從用戶端應用程式獲得語音優先虛擬助理體驗。 應用程式是利用[語音 SDK NuGet 套件](https://aka.ms/csspeech/nuget)與 Microsoft Visual Studio 2017 或更新版本 (任何版本) 所建置的。

> [!NOTE]
> 通用 Windows 平台可讓您開發應用程式，以在任何支援 Windows 10 的裝置 (包括 PC、Xbox、Surface Hub 與其他裝置) 上執行。

## <a name="prerequisites"></a>必要條件

本快速入門需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更新版本
* 適用於語音服務的 Azure 訂用帳戶金鑰。 [免費取得一個金鑰](get-started.md)或 [Azure 入口網站](https://portal.azure.com)上建立金要。
* 先前建立且使用 [Direct Line Speech 頻道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)設定的 Bot

    > [!NOTE]
    > Direct Line Speech (預覽) 目前僅可在部分的語音服務區域使用。 請參閱[語音優先虛擬助理支援的區域清單](regions.md#voice-first-virtual-assistants)，並確定您的資源部署於其中一個區域。

## <a name="optional-get-started-fast"></a>選用：快速開始使用

本快速入門會逐步說明如何製作簡單的用戶端應用程式以連線到您具備語音功能的 Bot。 如果您想要深入了解，可以在[語音 SDK 範例](https://aka.ms/csspeech/samples)的 `quickstart` 資料夾之下取得本快速入門中使用的完整、可立即編譯的原始程式碼。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 應用程式的使用者介面可使用 XAML 來定義。 在方案總管中開啟 `MainPage.xaml`。 在設計工具的 XAML 檢視中，以下面的內容取代整個內容。

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. 開啟程式碼後置原始檔`MainPage.xaml.cs`。 您會發現 `MainPage.xaml` 以下的項目均已分組。 使用以下列程式碼取代內容。 以下是此範例的涵蓋內容：

    * 在 Speech 和 Speech.Dialog 命名空間中使用陳述式
    * 進行簡單的實作，確認麥克風存取正常並連線至按鈕處理常式
    * 應用程式中的基本 UI 協助程式會顯示訊息及錯誤
    * 初始程式碼路徑登陸點，稍後會填入資訊
    * 協助程式會播放文字轉換語音資訊 (無需串流支援)
    * 稍後會填入即將開始接聽的空白按鈕處理常式

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. 接下來，您將使用您的訂用帳戶資訊建立 `DialogServiceConnector`。 將下列內容新增到 `InitializeDialogServiceConnector` 的方法主體，並針對您的 Bot、語音訂用帳戶和[區域](regions.md)，以您自己的值取代字串 `YourChannelSecret`、`YourSpeechSubscriptionKey` 和 `YourServiceRegion`。

    > [!NOTE]
    > Direct Line Speech (預覽) 目前僅可在部分的語音服務區域使用。 請參閱[語音優先虛擬助理支援的區域清單](regions.md#voice-first-virtual-assistants)，並確定您的資源部署於其中一個區域。

    > [!NOTE]
    > 如需設定 Bot 和擷取頻道祕密的詳細資訊，請參閱 [Direct Line Speech 頻道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)的 Bot Framework 文件。

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region. Note: only a subset of regions are currently supported

    var botConfig = DialogServiceConfig.FromBotSecret(channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

1. `DialogServiceConnector` 依賴多個事件來傳達其 Bot 活動、語音辨識結果及其他資訊。 新增這些事件的處理常式，並將下列內容附加至 `InitializeDialogServiceConnector` 的方法主體結尾。

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };
    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. 建立組態和註冊事件處理常式後，`DialogServiceConnector` 現在只需要接聽。 將下列程式碼新增至 `MainPage` 類別中的 `ListenButton_ButtonClicked` 方法。

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (connector == null)
        {
            InitializeDialogServiceConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = connector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = connector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await connector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. 儲存專案的所有變更。

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 建置應用程式。 從 Visual Studio 的功能表列中，選取 [建置]   > [建置解決方案]  。 現在會編譯程式碼，而且不會出現任何錯誤。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [建置解決方案] 選項已醒目提示](media/sdk/qs-csharp-uwp-08-build.png "成功建置")

1. 啟動應用程式。 從 Visual Studio 的功能表列中，選取 [偵錯]   > [開始偵錯]  ，或按 **F5**。

    ![Visual Studio 應用程式的螢幕擷取畫面，其中 [開始偵錯] 選項已醒目提示](media/sdk/qs-csharp-uwp-09-start-debugging.png "啟動應用程式並進行偵錯")

1. 此時會出現快顯視窗。 在您的應用程式中，選取 [啟用麥克風]  ，並確認彈出的權限要求。

    ![權限要求的螢幕擷取畫面](media/sdk/qs-csharp-uwp-10-access-prompt.png "啟動應用程式進入偵錯模式")

1. 選取 [與您的 Bot 交談]  ，然後對裝置的麥克風說出英文片語或句子。 您的語音會傳送到 Direct Line Speech 頻道並轉譯為文字，該文字會出現在視窗中。

    ![成功 Bot 回合的螢幕擷取畫面](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "成功 Bot 回合")

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立及部署基本 Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>另請參閱

- [關於語音優先虛擬助理](voice-first-virtual-assistants.md)
- [免費取得語音服務的訂用帳戶金鑰](get-started.md)
- [自訂喚醒字](speech-devices-sdk-create-kws.md)
- [將 Direct Line Speech 連線至 Bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [探索 GitHub 上的 C# 範例](https://aka.ms/csspeech/samples) \(英文\)
