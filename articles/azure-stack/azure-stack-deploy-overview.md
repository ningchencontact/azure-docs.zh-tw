---
title: "評估 Azure Stack 開發套件 | Microsoft Docs"
description: "了解如何部署評估用的 Azure Stack 開發套件。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>快速入門：評估 Azure Stack 開發套件

適用於：Azure Stack 開發套件

[Azure Stack 開發套件](azure-stack-poc.md)是一個測試和部署環境，可供您部署以評估及示範 Azure Stack 的功能和服務。 若要讓它啟動並執行，您將需要準備環境硬體並執行一些指令碼 (這將需要數小時的時間)。 之後，您可以登入系統管理員和使用者入口網站，以管理 Azure Stack 和測試供應項目。 

1. [**規劃您的硬體、軟體及網路**](azure-stack-deploy.md)。 裝載開發套件的電腦 (開發套件主機) 必須符合硬體、軟體及網路需求。 此外，您還必須在使用 Azure Active Directory 或「Active Directory 同盟服務」之間做選擇。 開始部署之前，請務必符合這些先決條件，如此安裝程序才能順暢執行。 

2. [**下載部署套件並解壓縮**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit)。 您可以將部署套件下載到開發套件主機或另一部電腦。 解壓縮後的部署檔案會佔用 60 GB 的可用磁碟空間，因此使用另一部電腦可協助降低開發套件主機的硬體需求。

3. 使用安裝程式來[**準備開發套件主機**](azure-stack-run-powershell-script.md)。 在此步驟之後，開發套件主機會開機進入 Cloudbuilder.vhdx (一個包含可開機作業系統和 Azure Stack 安裝檔的虛擬硬碟)。

4. 在開發套件主機上[**部署開發套件**](azure-stack-run-powershell-script.md)。

5. 如果您的 Azure Stack 部署使用 Azure Active Directory，您就必須[向 Azure 註冊 Azure Stack](azure-stack-register.md)，如此您才能夠[將 Azure Marketplace 項目下載](azure-stack-download-azure-marketplace-item.md)到 Azure Stack。

完成這些步驟之後，您將會擁有一個同時具有系統管理員和使用者入口網站的開發套件環境。 接著，您可以[連線並登入](azure-stack-connect-azure-stack.md)入口網站。 然後，您可以開始部署資源提供者、建立[供應項目](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions)，以及填入 Azure Stack [市集](azure-stack-marketplace.md)。
