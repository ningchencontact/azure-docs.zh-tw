---
title: 警示驗證 （EICAR 測試檔案） 在 Azure 資訊安全中心 |Microsoft Docs
description: 本文件可協助您在 Azure 資訊安全中心驗證安全性警示。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626308"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Azure 資訊安全中心的警示驗證 （EICAR 測試檔案）
這份文件可協助您了解如何驗證您的系統是否已針對 Azure 資訊安全中心警示正確設定。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
警示是當它偵測到您的資源上的威脅時，資訊安全中心會產生通知。 其設定優先權和列出的警示，以及讓您快速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。
如需詳細資訊，請參閱 < [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)和[管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>警示驗證

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## 驗證 Windows VM 上的警示 <a name="validate-windows"></a>

之後您的電腦安裝代理程式的資訊安全中心，請遵循下列步驟從電腦所在的受攻擊資源的警示：

1. 複製可執行檔 (例如**calc.exe**) 到電腦的桌面或其他目錄的方便起見，並將它重新命名**ASC_AlertTest_662jfi039N.exe**。
1. 開啟命令提示字元並執行此檔案的引數 （只是假的引數名稱），例如： ```ASC_AlertTest_662jfi039N.exe -foo```
1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 類似於警示[範例](#alert-validate)下方應該顯示：

> [!NOTE]
> 檢閱 Windows 此測試警示時，請確定欄位**啟用引數稽核**是 **，則為 true**。 如果它是**false**，則您必須啟用稽核的命令列引數。 若要啟用它，請使用下列命令列：
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## 驗證在 Linux VM 上的警示 <a name="validate-linux"></a>

之後您的電腦安裝代理程式的資訊安全中心，請遵循下列步驟從電腦所在的受攻擊資源的警示：
1. 將可執行檔複製到方便存取的位置，並重新命名為 **。 / asc_alerttest_662jfi039n**，例如：

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. 開啟命令提示字元並執行此檔案：

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. 等待 5 到 10 分鐘，然後開啟安全性中心警示。 類似於警示[範例](#alert-validate)下方應該顯示：

### 警示範例 <a name="alert-validate"></a>

![警示驗證範例](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>另請參閱
本文介紹警示驗證程序。 現在，您已熟悉此驗證，請嘗試下列文章︰

* [管理及回應 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何在資訊安全中心管理警示，以及回應安全性事件。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [了解 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同類型的安全性警示。
* [Azure 資訊安全中心疑難排解指南](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide)。 了解如何針對資訊安全中心的常見問題進行疑難排解。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
