---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461507"
---
## <a name="configure-an-environment-variable-for-authentication"></a>設定環境變數來進行驗證

應用程式需要存取它們所使用的認知服務進行驗證。 若要驗證，我們建議您建立的環境變數，以將金鑰儲存在您的 Azure 資源。 

您的金鑰之後，它寫入新的環境變數執行應用程式的本機電腦上。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 取代`your-key`使用您的異常偵測器的存取金鑰：

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    新增環境變數之後，您可能需要重新啟動任何需要讀取環境變數的執行中程式，包括主控台視窗。 例如，如果您使用 Visual Studio 為您的編輯器，再重新啟動 Visual Studio 執行此範例。

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    新增環境變數之後，從主控台視窗執行 `source ~/.bashrc`，讓變更生效。
    
* macOS
    
    編輯 .bash_profile，然後新增環境變數：
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    新增環境變數之後，從主控台視窗執行 `source .bash_profile`，讓變更生效。
