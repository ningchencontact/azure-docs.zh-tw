---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841494"
---
## <a name="configure-an-environment-variable-for-authentication"></a>設定環境變數以進行驗證

應用程式需要驗證其所使用之認知服務的存取權。 若要進行驗證, 我們建議您建立環境變數來儲存 Azure 資源的金鑰。 

擁有金鑰之後, 請在執行應用程式的本機電腦上, 將它寫入新的環境變數。 若要設定環境變數，請開啟主控台視窗，並遵循您的作業系統所適用的指示。 將`your-key`取代為您資源的其中一個金鑰。

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    新增環境變數之後，您可能需要重新啟動任何需要讀取環境變數的執行中程式，包括主控台視窗。 例如，如果您使用 Visual Studio 做為編輯器，請在執行範例前重新啟動 Visual Studio。

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
