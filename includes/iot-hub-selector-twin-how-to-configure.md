> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-how-to-configure.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-how-to-configure.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>簡介

在[開始使用 IoT 中樞裝置對應項][lnk-twin-tutorial]中，您已學會如何使用「標記」來設定裝置中繼資料。 您使用了「回報的屬性」接收來自裝置應用程式的裝置狀況，然後使用類似 SQL 的語言來查詢此資訊。

本教學課程將說明如何使用裝置對應項的「所需屬性」和「回報的屬性」遠端設定裝置應用程式。 裝置對應項中回報的屬性和所需屬性會啟用裝置應用程式的多步驟設定，並在所有裝置上提供此作業狀態的可見性。 您可以在[使用 IoT 中樞的裝置管理概觀][lnk-dm-overview]中，找到關於裝置組態所扮演角色的詳細資訊。

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

概括而言，使用裝置對應項讓解決方案後端能夠指定受控裝置所需的設定，而不是傳送特定的命令。 裝置負責設定更新其設定的最佳方式 (在特定裝置狀況會影響立即執行特定命令能力的 IoT 案例中很重要)，同時持續回報更新程序的目前狀態和潛在錯誤狀況。 這個模式對於大量裝置的管理很有幫助，因為它可讓解決方案後端具有所有裝置上設定程序狀態的完整可見性。

> [!TIP]
> 在裝置是以更為互動的方式進行控制的案例中 (例如，從使用者控制的應用程式開啟風扇)，請考慮使用[直接方法][lnk-methods]。

在本教學課程中，解決方案後端會變更目標裝置的遙測設定，使裝置應用程式能夠套用設定更新。 例如，設定更新需要重新啟動一個軟體模組，而本教學課程會使用簡單延遲加以模擬。

解決方案後端會以下列方式在裝置對應項的所需屬性中儲存設定：

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

因為設定可以是複雜物件，所以系統會為它們指派唯一識別碼 (雜湊或 [GUID][lnk-guid])。


裝置應用程式會在報告屬性中報告其目前組態鏡像所需屬性 **telemetryConfig**︰

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

請注意報告 **telemetryConfig** 如何具有額外屬性 **status**，用來報告設定更新程序的狀態。

收到新的所需設定時，裝置應用程式會透過變更狀態來回報擱置的設定：

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "configId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "configId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

接著，在稍後的時間，裝置應用程式會透過更新屬性來回報此作業成功或失敗。 解決方案後端可隨時查詢所有裝置上設定程序的狀態。

本教學課程說明如何：

* 建立模擬裝置應用程式，接收來自解決方案後端的組態更新，並且將多個更新報告為組態更新程序上的「報告屬性」。
* 建立後端應用程式，更新裝置的所需組態，然後查詢組態更新程序。

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier
