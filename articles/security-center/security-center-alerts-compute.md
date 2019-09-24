---
title: Azure 資訊安全中心中的雲端原生計算威脅偵測 |Microsoft Docs
description: 本主題提供 Azure 資訊安全中心中可用的雲端原生計算警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: memildin
ms.openlocfilehash: c3fcbadf93ff72f7d2a1dca3b25ace81c9d4f1ae
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202621"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Azure 資訊安全中心中的雲端原生計算威脅偵測

身為雲端提供者，Azure 資訊安全中心會使用其所需的獨特可見度來分析內部記錄，並找出多個目標的攻擊方法。 本主題提供適用于下列 Azure 服務的警示:

* [Azure App Service](#app-services)
* [Azure Container Service](#azure-containers) 

## Azure App Service<a name="app-services"></a>

資訊安全中心使用雲端的規模來識別以 App Service 執行之應用程式為目標的攻擊。 Web 應用程式在新式網路中很常見，攻擊者會探查以找出這些弱點和入侵漏洞。 在路由傳送至特定環境之前，對 Azure 中執行之應用程式的要求會經歷數個閘道，並在其中進行檢查和記錄。 接著會使用此資料來識別入侵和攻擊者，並瞭解稍後將使用的新模式。

藉由使用 Azure 做為雲端提供者的可見度，資訊安全中心分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，方法包含廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自于一小部分的 Ip，並展現對多部主機上相似端點的編目模式。 攻擊會搜尋易受攻擊的頁面或外掛程式，而無法從單一主機的觀點來識別。

資訊安全中心也可以存取基礎沙箱和 Vm。 除了記憶體的辯論外, 基礎結構也可以告訴故事, 從攻擊者的新攻擊, 到客戶電腦的危害。 因此, 資訊安全中心可以在遭到入侵之後, 偵測對 web 應用程式的攻擊。

> [!div class="mx-tableFixed"]

|警示|描述|
|---|---|
|**偵測到可疑的 WordPress 主題調用**|App Service 活動記錄指出您 App Service 資源上可能的程式碼插入活動。<br/> 此可疑活動類似于操作 WordPress 主題以支援伺服器端程式碼執行的活動，接著是直接的 web 要求，用來叫用操作的主題檔案。 這種類型的活動可以是透過 WordPress 的攻擊活動的一部分。|
|**偵測到從異常 IP 位址到網頁的連線**|[App Service 活動記錄] 表示與先前從未連接過之來源位址的機密網頁連接。 這可能表示有人正嘗試對您的 web 應用程式管理頁面進行暴力密碼破解攻擊。 這也可能是合法使用者使用新 IP 位址的結果。|
|**在威脅情報中找到連線到您 Azure App Service FTP 介面的 IP**|App Service FTP 記錄分析偵測到從威脅情報摘要中找到的來源位址連接。 在此連接期間，使用者會存取列出的頁面。|
|**偵測到 Web 指紋**|App Service 活動記錄指出您 App Service 資源上可能的 web 指紋活動。 <br/>此可疑活動與稱為「盲人大象」的工具相關聯。 工具指紋 web 伺服器, 並嘗試偵測已安裝的應用程式及其版本。 攻擊者通常會使用此工具來探查 web 應用程式, 以找出弱點。|
|**偵測到可能有弱點之 web 網頁的可疑存取**|App Service 活動記錄表示已存取看似機密的網頁。 <br/>此可疑活動源自其存取模式類似于 web 掃描器的來源位址。 這類活動通常與攻擊者嘗試掃描您的網路以嘗試存取敏感性或易受攻擊的網頁有關。|
|**上傳資料夾中的 PHP 檔案**|App Service 活動記錄指出有某個專案已存取上傳資料夾中的可疑 PHP 頁面。 <br/>這種類型的資料夾通常不會包含 PHP 檔案。 存在此類型的檔案可能表示利用了任意檔案上傳弱點的攻擊。|
|**嘗試在 Windows App Service 上執行 Linux 命令**|分析 App Service 處理常式偵測到嘗試在 Windows App Service 上執行 Linux 命令。 此動作正由 web 應用程式執行。 在惡意探索通用 web 應用程式中的弱點的行銷活動期間，通常會出現這種行為。|
|**偵測到可疑的 PHP 執行**|電腦記錄表示可疑的 PHP 進程正在執行。 此動作包含嘗試從命令列執行作業系統命令或 PHP 程式碼，方法是使用 PHP 進程。 雖然此行為可能是合法的，但在 web 應用程式中，這種行為可能表示惡意活動，例如嘗試使用 web shell 來感染網站。|
|**從暫存資料夾執行進程**|App Service 進程分析偵測到從應用程式的暫存資料夾執行進程。 雖然此行為可能是合法的，但在 web 應用程式中，這種行為可能表示惡意活動。|
|**偵測到嘗試執行高許可權命令**|分析 App Service 處理常式偵測到嘗試執行需要高許可權的命令。 命令會在 web 應用程式內容中執行。 雖然此行為可能是合法的，但在 web 應用程式中，這種行為可能表示惡意活動。|

> [!NOTE]
> App Service 的資訊安全中心威脅偵測目前無法在 Azure 政府和主權雲端區域中使用。

## Azure Container Service<a name="azure-containers"></a>

資訊安全中心在以 auditd 架構為基礎的 Linux 電腦上，為您的容器提供即時威脅偵測。 這些警示會識別數個可疑的 Docker 活動，例如在主機上建立具特殊許可權的容器、指示在 Docker 容器內執行的安全殼層（SSH）伺服器，或使用加密採礦人員。 

您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。 除了 Linux 偵測之外, 資訊安全中心也會提供更具體的分析, 供容器部署之用。
