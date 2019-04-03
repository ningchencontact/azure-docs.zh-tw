---
title: Azure 資訊安全中心的常見問題 IoT 預覽 |Microsoft Docs
description: 尋找最常見問題集解答有關 Azure 資訊安全中心的 IoT 功能和服務。
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: cd9e77e09e2a2fd5fe1108e98da028d26fc623c7
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863044"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>IoT 的 azure 資訊安全中心常見問題集  

> [!IMPORTANT]
> IoT 的 azure 資訊安全中心目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章提供 IoT 一份常見問題與解答有關 Azure 資訊安全中心 (ASC)。 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 提供支援 IoT 安全性？

Azure 提供整合式的檢視，監視及管理您的 IoT 安全性，您透過 Azure 資訊安全中心的整體安全性解決方案的一部分。 如果您是應用程式開發人員，您可以使用 IoT 中樞檢視來管理您的 IoT 應用程式安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>什麼是 Azure 的獨特的價值主張，IoT 安全性？

Iot 的 ASC 可讓企業能夠擴充其現有網路安全性檢視，以其完整的 IoT 解決方案。 Azure 提供的端對端檢視您的商務解決方案，讓您可以採取商務相關的動作和決策會根據您企業的安全性狀態，和收集資料。 結合安全性使用 Azure IoT、 Azure IoT Edge、 Azure Sphere、 Azure Central 和 Azure 資訊安全中心可讓您建立您要的方案，您需要的安全性。

## <a name="who-is-asc-for-iot-made-for"></a>誰是 IoT 對 ASC？ 

Iot 的 ASC 整合至 Azure IoT 中樞安全性，並提供管理每日商務解決方案的安全性作業。 Iot 的 ASC 也會整合到 Azure 資訊安全中心功能，並提供整合式的檢視來監視和管理您的 IoT 安全性，您整體的安全性解決方案的一部分。

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>ASC iot 如何比較出競賽？

其他解決方案則是提供一組功能，可讓客戶建立自己的解決方案，iot ASC 提供唯一端對端 IoT 安全性解決方案，提供寬型檢視中的所有相關 Azure 資源的安全性。 Azure 可讓快速部署和使用 IoT 中樞模組對應項，很容易與現有的裝置管理工具整合的完整整合。

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>必須是 Azure 資訊安全中心 (ASC) 客戶嗎？

否，但建議。 ASC、 iot ASC 接收已連線的資源的有限的資料而不提供有限的分析的潛在攻擊面、 威脅，以及潛在的攻擊。 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>必須是 Azure IoT 客戶嗎？

是。 Iot 的 ASC 是依賴 Azure IoT 連線和基礎結構而定。

## <a name="do-i-have-to-install-an-agent"></a>必須安裝代理程式嗎？

在您的 IoT 裝置上的代理程式安裝不是以啟用 Microsoft ASC iot 必要的。 您可以選擇下列三個選項，取得不同層級的安全性監視和管理功能，根據您的選擇：

1. IoT 安全性代理程式 ASC 使用或安裝不需要修改。 此選項提供最高層級的增強式的安全性深入裝置行為及存取權。

2. 建立您自己的代理程式，並實作 Microsoft ASC 的 IoT 安全性訊息結構描述。 此選項可讓 IoT 分析工具，在您的裝置安全性代理程式上的 Microsoft ASC 的使用方式。

3. 在您的 IoT 裝置上沒有安全性代理程式安裝。 此選項可讓 IoT 中樞通訊監視，以降低的安全性監視和管理功能。 

## <a name="what-does-the-asc-for-iot-agent-do"></a>ASC IoT 代理程式有何作用？

ASC IoT 代理程式提供裝置組態、 行為，和存取權 （藉由掃描 configuration）、 程序和連線的裝置層級威脅的涵蓋範圍。 IoT 安全性代理程式 ASC 不會掃描商務相關的資料或活動。

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>哪裡可以取得 ASC IoT 安全性代理程式？

IoT 安全性代理程式 ASC 是開放原始碼和可在 GitHub 上的 32 位元和 64 位元 Windows 和 Linux 版本： https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>其中 ASC IoT 代理程式會安裝？ 

詳細的安裝和代理程式部署資訊可在 GitHub 中： https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>相依性和代理程式的必要條件為何？

ASC iot 支援各種平台。 請參閱[支援的裝置平台](how-to-deploy-agent.md)來驗證您的特定裝置的支援。 

## <a name="which-data-is-collected-by-the-agent"></a>代理程式會收集哪些資料？

連線、 存取、 防火牆設定、 處理程序清單與 OS 基準會收集代理程式。

## <a name="how-much-data-will-the-agent-generate"></a>代理程式將會產生多少資料？

裝置、 應用程式、 連線類型，以及代理程式的客戶組態會隨代理程式資料產生。 由於裝置與 IoT 解決方案之間有高變化，建議您先部署實驗室或測試設定來觀察、 了解，並設定符合您的需求，在測量的產生的資料量的特定組態中的代理程式。 之後啟動服務，ASC IoT 代理程式會提供作業最佳化代理程式的輸送量來協助您設定和自訂程序的建議。

## <a name="how-can-i-control-my-billing"></a>我要如何控制我的帳單？

ASC iot 會提供可設定代理程式掃描、 資料緩衝區，以及建立自訂的警示會增加或減少的代理程式所產生的資料量的能力。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理程式訊息會佔用從 IoT 中樞的配額？

是。 代理程式傳輸資料將會在您的 IoT 中樞配額計算。 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下來呢？ 我已經安裝了代理程式，看不到任何活動或記錄檔

1. 檢查[代理程式類型符合指定的作業系統平台，您的裝置](how-to-deploy-agent.md)

1. 確認[代理程式會在裝置上執行](how-to-agent-configuration.md)。

2. 請檢查[服務已成功啟用](quickstart-onboard-iot-hub.md)要**安全性**在 IoT 中樞。 

3. 裝置的核取[設定在 IoT 中樞與 IoT 模組 ASC](quickstart-create-security-twin.md)。  

如果仍然無法使用活動或記錄檔，請連絡您的 ASC 的 IoT 合作夥伴，以取得其他協助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>網際網路連線停止運作時，會發生什麼事？

代理程式會繼續執行，並儲存資料，因為裝置正在執行。 資料會儲存在安全訊息快取，根據大小設定。 當裝置重新取得連線時，安全訊息會繼續傳送。 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>重新啟動裝置時，將安全性代理程式自我復原？

安全性代理程式被設計來自動重新執行每個裝置重新啟動。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理程式可能會影響裝置或其他已安裝的軟體的效能？

代理程式會耗用的機器資源與任何其他應用程式/處理序，並應該不會中斷正常的裝置活動。 執行代理程式在裝置上的資源耗用量會結合其安裝與設定。 我們建議以及與其他 IoT 應用程式和功能的互通性的受控制環境中測試您的代理程式設定，再嘗試部署在生產環境中。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我在裝置上進行某些維護。 我可以關閉代理程式嗎？

代理程式無法關閉。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>有一個方法來測試代理程式是否正確運作？ 

如果代理程式停止通訊，或無法傳送安全訊息**裝置為無訊息方式**就會產生警示。

## <a name="can-i-create-my-own-alerts"></a>我可以建立自己的警示嗎？

是。 您可以在組預先決定的行為，例如 IP 位址和開啟的連接埠上設定自訂的警示。 請參閱[建立自訂警示](quickstart-create-custom-alerts.md)若要深入了解自訂警示和如何讓它們。 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>哪裡可以查看記錄檔？ 我可以自訂記錄檔嗎？

- 檢視警示和建議使用您已連線的 Log Analytics 工作區。 在工作區設定儲存體大小和持續時間。

- 未經處理的資料，從您的安全性代理程式也可以儲存在 Log Analytics 帳戶中。 變更這個選項的設定之前，請考慮大小、 持續時間、 儲存體需求，以及相關聯的成本。 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>為什麼我應該將加入 ASC iot 模組身分識別？ 用途為何？

ASC IoT 模組用於代理程式設定和管理。


## <a name="next-steps"></a>後續步驟

若要深入了解如何開始使用 ASC iot，請參閱下列文章：


- 閱讀 IoT ASC[概觀](overview.md)
- 確認[服務必要條件](service-prerequisites.md)
- 深入了解如何[開始](getting-started.md)
- 了解[ASC IoT 安全性警示](concept-security-alerts.md)

