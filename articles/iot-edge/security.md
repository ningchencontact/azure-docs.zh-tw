---
title: 安全性架構 - Azure IoT Edge | Microsoft Docs
description: 深入了解用來開發 Azure IoT Edge 且應在設計解決方案時考量的安全性、驗證及授權標準
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 8aadddbc9ae13a87f89db4d7e7189ea7aa8aeef5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60612036"
---
# <a name="security-standards-for-azure-iot-edge"></a>適用於 Azure IoT Edge 的安全性標準

Azure IoT Edge 被設計來處理將資料與分析移到智慧型邊緣時的固有風險案例。 IoT Edge 安全性標準提供不同風險設定檔和部署案例的彈性，同時仍然提供您預期從所有 Azure 服務的保護。 

Azure IoT Edge 可在各種硬體廠牌和型號上執行、支援數種作業系統，且適用於各種部署案例。 在評估部署案例的風險時需要考量諸多事項，包括解決方案擁有權、部署地理位置、資料敏感度、隱私權、應用程式類別以及法規需求。 而不是提供具體的解決方案，針對特定案例，IoT Edge 是一種可延伸的安全性架構，專為小數位數的死板原則為基礎。 
 
本文會概述 IoT Edge 的安全性架構。 如需詳細資訊，請參閱[保護智慧邊緣](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) \(英文\)。

## <a name="standards"></a>標準

標準會提昇安全性和實作的簡便性，兩者都是安全性的特點。 安全性解決方案應該投入安全性的評估，來建置信任且不成為部署的障礙。 用來保護 Azure IoT Edge 的架構在設計時，是依據經過時間考驗及業界證實的安全性通訊協定，因此具有熟悉和重複使用特性。 

## <a name="authentication"></a>驗證

當您部署之 IoT 解決方案時，您必須知道受信任的動作項目、 裝置和模組，可以存取您的解決方案。 這類知識可提供參與者的安全責任。 Azure IoT Edge 透過驗證來獲得這項知識。 憑證型驗證是驗證適用於 Azure IoT Edge 平台的主要機制。 這項機制是從網際網路工程任務推動小組 (IETF) 的一組標準管理公開金鑰基礎結構 (PKiX) 衍生而來。     

所有與 Azure IoT Edge 裝置互動的裝置、模組和執行者 (不論是以實體還是網路來連線) 都應該要有唯一的憑證身分識別。 不過，並非每個案例或元件可能投入憑證式驗證。 在這些案例中，安全性架構的擴充性會讓您有其他的安全選擇。 

## <a name="authorization"></a>Authorization

最低權限原則的含意是，系統的使用者和元件只應具有執行其角色所需最小一組資源和資料的存取權。 裝置、模組和執行者只應存取其權限範圍內的資源和資料，且只有在架構上允許時才能存取。 某些權限可以設定具有足夠權限，而且其他人在架構上強制執行。  例如，您可以透過特殊權限設定來授權模組起始對於 Azure IoT 中樞的連線。 但一個 Azure IoT Edge 裝置中的模組沒有道理要存取另一個 Azure IoT Edge 裝置中的模組對應項。

其他授權配置包括憑證簽署權限和角色型存取控制 (RBAC)。 

## <a name="attestation"></a>證明

證明可確保軟體位元的完整性。  對於惡意程式碼的偵測與預防很重要。  Azure IoT Edge 安全性架構會將證明分類在三個主要類別之下：

* 靜態證明
* 執行階段證明
* 軟體證明

### <a name="static-attestation"></a>靜態證明

靜態證明會驗證裝置上所有軟體的完整性，包括作業系統、所有執行階段，以及裝置開啟時的設定資訊。 它通常稱為安全開機。 Azure IoT Edge 裝置的安全性架構會延伸到製造商，並且併入安全的硬體功能，以保障靜態證明程序。 這些程序包括安全開機和安全韌體升級。  與晶片廠商密切合作可以排除非必要的韌體層，藉此將威脅表面降至最低。 

### <a name="runtime-attestation"></a>執行階段證明

一旦系統已完成安全的開機程序並且啟動及執行，設計良好的系統就會偵測到嘗試插入惡意程式碼的企圖，並採取適當的因應對策。 惡意程式碼攻擊可能會針對系統的連接埠和介面可用來存取系統。 或者，如果惡意執行者可透過實體存取裝置，便有可能會竄改裝置本身，或使用旁路攻擊來取得存取權。 靜態證明無法偵測可能會在惡意程式碼或未經授權的組態變更的形式，這類惡意內容，因為它會插入在開機程序之後。 裝置硬體所提供或強制執行的因應對策有助於閃避這類威脅。  Azure IoT Edge 的安全性架構會明確呼叫擴充功能來對抗執行階段威脅。  

### <a name="software-attestation"></a>軟體證明

所有狀況良好的系統，包括智慧型邊緣系統必須接受修補程式和升級。  安全性對於更新處理程序很重要，否則會成為潛在威脅漏洞。  透過更新的 Azure IoT Edge 呼叫的安全性架構會測量，和簽署的封裝以確保完整性和驗證封裝的來源。  此一標準適用於所有作業系統和應用程式軟體位元。 

## <a name="hardware-root-of-trust"></a>硬體信任根源

對於許多智慧邊緣裝置來說，特別是潛在惡意執行者可實際存取裝置之處的那些裝置，裝置硬體的安全性是最後一道保護防線。 可防竄改的硬體對於這類部署非常重要。 Azure IoT Edge 股利安全晶片硬體廠商提供不同類別的硬體信任根源，以因應各種風險設定檔和部署案例。 硬體信任可能源自常見的安全性通訊協定標準，例如信賴平台模組 (ISO/IEC 11889) 和信賴運算群組的 Device Identifier Composition Engine (DICE)。 安全保護區技術 (例如，TrustZones 和 Software Guard Extensions (SGX)) 也能提供硬體信任。 

## <a name="certification"></a>認證

為了協助客戶在採購 Azure IoT Edge 裝置以進行部署時做出明智的決策，Azure IoT Edge 架構包含憑證需求。  這些需求的基本是有關於安全性宣告的憑證，和有關於安全性實作驗證的憑證。  例如，安全性宣告憑證會通知 IoT Edge 裝置使用已知的安全開機硬體來對抗開機攻擊。 驗證憑證會通知安全硬體已正確地實作以在裝置中提供這個值。  為求簡單，此架構會嘗試讓憑證保持最低的負擔。   

## <a name="extensibility"></a>擴充性

隨著 IoT 技術促成不同類型的企業轉型，安全性也應該同步演進，以應付新出現的案例。  Azure IoT Edge 安全性架構是從穩固基礎開始，在這個基礎上建置擴充性到不同的維度以納入： 

* 第一方安全性服務，例如 Azure IoT 中樞的裝置佈建服務。
* 第三方服務，例如不同應用程式類別 (像是工業或醫療) 的受控安全性服務，或是透過豐富合作夥伴網路的技術焦點 (像是網狀網路中的安全性監視或晶片硬體證明服務)。
* 舊版系統，以包含具有其他安全性策略的更動，例如使用憑證以外的安全技術來進行驗證和身分識別管理。
* 安全硬體，以便採用新興安全硬體技術和晶片合作夥伴貢獻。

最後，從保護 IoT 共同興趣驅動的開放社群之共用作業而來的智慧型邊緣保護是最成功的。  這些貢獻的形式可能是安全技術或服務。  Azure IoT Edge 安全性架構提供安全性的穩固基礎，可以針對涵蓋範圍最大值加以擴充，在智慧型邊緣中提供與 Azure 雲端相同等級的信任和完整性。  

## <a name="next-steps"></a>後續步驟

深入了解 Azure IoT Edge 如何[保護智慧邊緣](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) \(英文\)。
