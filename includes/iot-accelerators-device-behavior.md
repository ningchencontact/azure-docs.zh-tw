---
title: 包含檔案
description: 包含檔案
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444016"
---
## <a name="state-behavior"></a>狀態行為

裝置模型結構描述的 [模擬](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) 區段會定義模擬裝置的內部狀態：

- `InitialState` 會針對裝置狀態物件的所有屬性，定義初始值。
- `Script` 會識別排程執行以更新裝置狀態的 JavaScript 檔案。

下列範例會顯示模擬 Chiller 裝置之裝置狀態物件的定義：

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

模擬裝置的狀態 (如 `InitialState` 區段中所定義) 會由模擬服務保留在記憶體中。 狀態資訊會當作輸入，傳遞至 **chiller-01-state.js** 中定義的 `main` 函式。 在此範例中，模擬服務會每 5 秒執行 **chiller-01-state.js** 檔案一次。 指令碼可以修改模擬裝置的狀態。

以下示範典型 `main` 函式的外框：

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` 參數具有下列屬性：

- `currentTime`，作為格式為 `yyyy-MM-dd'T'HH:mm:sszzz` 的字串
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 參數包含裝置的狀態，如裝置模擬服務中所維護。 此值是先前對 `main` 的呼叫所傳回的 `state` 物件。

下列範例示範 `main` 方法的典型實作，以處理模擬服務所維護的裝置狀態：

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

下列範例會示範 `main` 方法如何模擬隨著時間而變化的遙測值：

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

您可以在 GitHub 上檢視完整的 [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js)。

## <a name="method-behavior"></a>方法行為

裝置模型結構描述的 [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) 區段會定義模擬裝置回應的方法。

下列範例會示範模擬的 Chiller 裝置所支援的方法清單：

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

每個方法都有實作方法行為的相關聯 JavaScript 檔案。

模擬裝置的狀態 (如結構描述的 `InitialState` 區段中所定義) 會由模擬服務保留在記憶體中。 呼叫方法時，狀態資訊會當作輸入，傳遞至 JavaScript 檔案中定義的 `main` 函式。 指令碼可以修改模擬裝置的狀態。

以下示範典型 `main` 函式的外框：

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` 參數具有下列屬性：

- `currentTime`，作為格式為 `yyyy-MM-dd'T'HH:mm:sszzz` 的字串
- `deviceId`，例如 `Simulated.Chiller.123`
- `deviceModel`，例如 `Chiller`

`state` 參數包含裝置的狀態，如裝置模擬服務中所維護。

`properties` 參數包含會以回報屬性的形式寫入至「IoT 中樞」裝置對應項的裝置屬性。

有三個全域函式可以用來協助實作此方法的行為：

- `updateState` 可更新模擬服務所保留的狀態。
- `updateProperty` 可更新單一裝置屬性。
- `sleep` 可暫停執行，以模擬長時間執行的工作。

下列範例顯示模擬的 Chiller 裝置所使用的縮寫版 **IncreasePressure method.js** 指令碼：

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>為指令碼檔案偵錯

您無法將偵錯工具附加至裝置模擬服務所使用的 Javascript 解譯器以執行狀態和方法指令碼。 不過，您可以在服務記錄檔中記錄資訊。 內建的 `log()` 函式可讓您儲存資訊以追蹤並偵錯函式的執行。

如果有語法錯誤，則解譯器會失敗，並將 `Jint.Runtime.JavaScriptException` 項目寫入至服務記錄檔。

GitHub 上的[在本機執行服務](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) \(英文\) 一文說明如何在本機執行裝置模擬服務。 在本機執行服務可以更輕鬆地為您模擬的裝置偵錯，再將其部署至雲端。