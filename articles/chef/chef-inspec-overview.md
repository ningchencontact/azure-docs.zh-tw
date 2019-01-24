---
title: 針對 Azure 基礎結構的合規性自動化使用 InSpec
description: 了解如何使用 InSpec 來偵測 Azure 部署中的問題
keywords: azure, chef, devops, virtual machines, overview, automate, inspec, 虛擬機器, 概觀, 自動化
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359216"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>針對 Azure 基礎結構的合規性自動化使用 InSpec
[InSpec](https://www.chef.io/inspec/) \(英文\) 是 Chef 的開放原始碼語言，描述可以在軟體工程師、作業和安全性工程師之間共用的安全性與合規性規則。 InSpec 的運作方式是將您基礎結構的實際狀態，與您在方便讀取和方便寫入的 InSpec 程式碼中所表示的理想狀態兩者進行比較。 InSpec 會偵測違規，並且以報告形式顯示調查結果，但可讓您控制補救措施。

您可以使用 InSpec 來驗證訂用帳戶內資源和資源群組的狀態，包括虛擬機器、網路設定、Azure Active Directory 設定等等。

本文說明使用 InSpec 讓 Azure 上的安全性和合規性更簡單的優點。

## <a name="make-compliance-easy-to-understand-and-assess"></a>讓合規性易於了解和評估
撰寫在試算表或 Word 文件中的合規性文件使要求有待解釋。 使用 InSpec，您就可以將需求轉換為可建立版本、可執行、人類看得懂的程式碼。 程式碼能取代應該要評估哪些項目的討論，有利於具有明確意圖的有形測試。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>偵測全面的問題並且對其補救措施設定優先權
InSpec 的無代理程式偵測模式可讓您快速大規模評估您的暴露等級。 影響/嚴重性評分的內建中繼資料，可協助判斷要著重哪個區域的補救措施。 您也可以快速地撰寫規則並立即推出，以回應新的弱點或法規。

## <a name="satisfy-audits"></a>滿足稽核要求
有了 InSpec，您就可以隨時回應稽核問題，而不只是以每季或每年等預先決定的間隔來回應。 藉由持續執行 InSpec 測試，您會進入稽核循環，了解您確切的合規性狀態和記錄，而不是對稽核員的發現感到驚訝。

## <a name="next-steps"></a>後續步驟

* 在 Azure Cloud Shell [![啟動 Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "啟動 Cloud Shell")](https://shell.azure.com) 中試用 InSpec
