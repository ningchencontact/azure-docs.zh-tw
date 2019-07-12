---
title: 威脅偵測的雲端原生計算，Azure 資訊安全中心 |Microsoft Docs
description: 本主題提供雲端原生計算，Azure 資訊安全中心可用的警示。 Azure 資訊安全中心。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571682"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>威脅偵測雲端原生的計算，Azure 資訊安全中心

身為雲端提供者中，資訊安全中心會利用它對分析內部的記錄檔，並找出多個目標的攻擊方法唯一的可見性。 本主題提供適用於下列 Azure 服務的警示：

* [Azure App Service](#app-services)
* [容器](#azure-containers) 

## Azure App Service <a name="app-services"></a>

資訊安全中心會利用雲端來識別攻擊的目標客戶透過 Azure App Service 執行的應用程式的規模。 幾乎在任何新式的網路中的 web 應用程式，攻擊者會探查來尋找這些，並利用的弱點。 再路由傳送至特定環境，在 Azure 中執行的應用程式的要求會經過數個閘道，它們會在其中檢查和記錄。 然後使用此資料，來識別入侵，攻擊者，以及了解稍後將使用的新模式。

利用 Azure 為雲端提供者有可見性，資訊安全中心會分析來識別攻擊方法，在多個目標上的 App Service 內部記錄。 例如，廣泛的掃描和分散式的攻擊。 這種攻擊通常來自一小部分的 Ip，並展現模式的多個主機上的搜尋易受攻擊的頁面或外掛程式編目類似的端點。 這可以使用雲端來偵測，但不能從單一主機的觀點來看進行識別。

資訊安全中心也會有存取權的基礎沙箱和 Vm。 記憶體鑑識調查，以及基礎結構可以表達意涵，從新的攻擊來入侵客戶機器在現實世界中流通。 因此，資訊安全中心可以偵測攻擊很長的 web 應用程式之後遭盜用。

> [!div class="mx-tableFixed"]

|警示|說明|
|---|---|
|**偵測到的可疑 WordPress 佈景主題叫用**|Azure App Service 活動記錄檔會指出您的 App Service 資源上可能的程式碼資料隱碼攻擊活動。<br/> 可疑的活動類似於操作 WordPress 佈景主題，以支援伺服器端程式碼，接著呼叫受操控的佈景主題檔案的直接存取的 web 要求執行的活動。 這種類型的活動，曾在過去攻擊活動的一部分移轉 WordPress。|
|**從異常偵測到的 IP 位址連線到網頁**|Azure App Service 活動記錄檔會指出敏感的 web 網頁的連線，從來源位址永遠不會連線到它之前。 這可能表示有人正嘗試暴力破解攻擊到您的 Web 應用程式管理頁面。 它也可能是合法的使用者正在使用新的 IP 位址的結果。|
|**威脅情報中找不到連接到您 Azure App Service 的 FTP 介面的 IP**|Azure App Service FTP 記錄檔分析偵測到威脅情報摘要中找不到來源位址的連接。 這在連接期間，使用者會存取下面所列的頁面。|
|**Web 記錄特徵偵測到**|Azure App Service 活動記錄檔指出可能 web 指紋識別您的 App Service 資源上的活動。 <br/>呼叫視力不良的 Elephant 的工具，此偵測到的可疑活動會產生關聯。 此工具指紋 web 伺服器，並嘗試偵測已安裝的應用程式和其版本。 攻擊者通常會使用探查 web 應用程式這項工具來找出弱點。|
|**可疑的存取權可能易受攻擊偵測到的 web 網頁**|Azure App Service 活動記錄檔會指出已存取 似乎是機密的網頁。 <br/>此可疑活動，源自於其的存取模式類似於 web 掃描器的來源位址。 這種活動通常是與嘗試掃描您嘗試及取得機密或易受攻擊的 web 網頁的存取權的網路攻擊者相關聯。|
|**上傳資料夾中的 PHP 檔案**|Azure App Service 活動記錄檔會指出項目已上傳資料夾中的可疑 PHP 頁面來存取。 <br/>這種類型的資料夾通常不包含 PHP 檔案。 這種類型的檔案存在可能表示的任意檔案上傳漏洞入侵。|
|**嘗試在 Windows 應用程式服務上執行 Linux 命令**|App Service 的程序的分析偵測到嘗試將 Windows App Service 上執行 Linux 命令。 Web 應用程式執行此動作。 這種行為經常會看到的探索常見的 web 應用程式中的弱點行銷活動期間。|
|**偵測到可疑的 PHP 執行**|機器記錄檔，以表示可疑 PHP 處理序正在執行。 動作包含嘗試從命令列使用 PHP 程序執行作業系統命令或 PHP 程式碼。 雖然這種行為可能是合法的在 web 應用程式中這種行為也觀察到惡意活動，例如嘗試感染網站，web 介面的使用者。|
|**從暫存資料夾的程序執行**|App Service 的程序分析偵測到應用程式的暫存資料夾的程序的執行。 雖然這種行為可能是合法的在這種行為也觀察到惡意活動的 web 應用程式。|
|**嘗試執行偵測到的較高權限命令**|App Service 的程序的分析偵測到嘗試執行需要較高權限的命令。 在 web 應用程式內容中，執行命令。 雖然這種行為可能是合法的在這種行為也觀察到惡意活動的 web 應用程式。|

> [!NOTE]
> 資訊安全中心威脅偵測的 App Service 目前不是適用於 Azure government 和主權雲端區域。

如需有關 App Service 的威脅偵測警示會保護 App Service 與 Azure 資訊安全中心，請瀏覽，並檢閱如何啟用監視和保護您的應用程式服務工作負載。

## 容器 <a name="azure-containers"></a>

資訊安全中心會提供適用於您容器的即時威脅偵測 auditd framework 為基礎的 Linux 機器上。 警示會識別數個可疑 Docker 活動，例如特殊權限的容器在主機上，執行 Docker 容器或使用的密碼編譯採礦者內的安全殼層 (SSH) 伺服器的指示建立。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。 除了 Linux 偵測資訊安全中心也會提供更特定的容器部署的分析。
