---
title: Azure Stack 資料中心整合逐步解說 | Microsoft Docs
description: 了解資料中心內一個成功的 Azure Stack 現場部署應具備什麼。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: ad58b8f1034a0e2818d3aca53c023736da9370cb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119916"
---
# <a name="azure-stack-datacenter-integration"></a>Azure Stack 資料中心整合

本文將說明端對端 Azure Stack 客戶從購買整合式系統到解決方案提供者成功完成現場部署的體驗。 使用此資訊來簡化您的旅程，並協助為您 (一位 Azure Stack 客戶) 設定期望。

身為 Azure Stack 客戶，您應預期有下列階段：

|     |規劃階段|訂購程序|前置部署|處理站程序|硬體寄送|現場部署|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|與合作夥伴洽談以提供售前支援。|準備所需的軟體授權和合約。|提供客戶收集資料中心整合需求及文件的必要工具。|每月提供最新基準組建和工具鏈更新。|N/A|Microsoft 支援工程師會針對任何部署問題提供協助。|
|**合作夥伴**|根據客戶需求建議解決方案選項。<br><br>如有必要，提出概念證明 (POC)。<br><br>建立商業關係。<br><br>決定支援層級。|準備給客戶的必要合約。<br><br>建立客戶的購買訂單。<br><br>決定遞送時間軸。<br><br>如有必要，透過 Microsoft 與客戶聯繫。|提供客戶所需的訓練，以確保客戶了解所有部署必要條件和資料中心整合選項。<br><br>協助客戶驗證所收集的資料，以確保完整性和正確性。|套用最後一個已驗證的基準組建。<br><br>套用必要的 Microsoft 部署工具組。|將硬體寄送到客戶端。|由現場工程師處理部署事宜。<br><br>機架安裝。<br><br>硬體生命週期主機 (HLH) 部署。<br><br>Azure Stack 部署。<br><br>遞交給客戶。|
|**客戶**|描述預定的使用案例，並指定需求。|決定要使用的計費模型，並檢閱及核准合約。|完成部署工作表，並確定所有部署必要條件都符合且已準備好進行部署。|N/A|準備資料中心，確保具備所有必要的電源和冷卻系統、邊界連線能力，以及其他必要的資料中心整合需求。|可在部署期間提供訂用帳戶認證，並在所提供的資料有疑問時提供支援。|
| | | | | | | |


## <a name="planning-phase"></a>規劃階段
在規劃階段中，Microsoft 或 Azure Stack 解決方案合作夥伴會與您一起評估並了解您的需求，以判斷 Azure Stack 解決方案是否是適合您：

他們將協助您決定下列事項：

-   Azure Stack 是適合您組織的解決方案嗎？

-   您將需要何種大小的解決方案？

-   您的組織適用何種計費和授權模型？

-   電源和冷卻需求為何？

若要確保硬體解決方案會完美符合您的需求，可在購買前的規畫中使用 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)，來協助您判斷 Azure Stack 硬體解決方案適用的容量和設定。

該試算表的目的並「不是」取代您自己對硬體解決方案配適度的調查與分析。 在規劃 Azure Stack 部署時，您也應該檢閱適用於 Azure Stack 整合系統的[一般資料中心整合考量](azure-stack-datacenter-integration.md)。

## <a name="order-process-phase"></a>訂購程序階段
在這個階段中，很多關於可行性方面的問題已有解答。 現在，您已準備好要完成 Azure Stack 的購買，在簽署所有必要的合約及購買訂單後，您必須提供整合需求資料給解決方案提供者。

## <a name="pre-deployment-phase"></a>前置部署階段
在這個階段中，您必須決定要如何將 Azure Stack 整合到您的資料中心。 為了簡化此程序，Microsoft 會與解決方案提供者共同作業，匯集需求範本來協助您收集必要資訊，以規劃您環境內的整合式系統部署。

[一般資料中心整合考量](azure-stack-datacenter-integration.md)文章會提供可協助您完成範本 (稱為部署工作表) 的資訊。 

> [!IMPORTANT]
> 在這個階段中，務必先調查並決定好所有必要資訊，然後才訂購解決方案。 請注意，這個步驟相當耗時，需要與您組織中的多個專業領域協調，以及從中收集資料。 不正確或不完整的資訊可能導致部署時間過長。 

在前置部署階段，您必須決定下列事項：

- **Azure Stack 連線模型和識別提供者**。 您可以選擇部署 Azure Stack 以[連線網際網路 (和 Azure)，或者中斷連線](azure-stack-connection-models.md)。 若要發揮 Azure Stack 的最大功效，包括混合式情節，您可以讓部署連線至 Azure。 選擇 Active Directory 同盟服務 (AD FS) 或 Azure Active Directory (Azure AD) 是您必須在部署期間進行的一次性決策。 **若未重新部署整個系統，之後便無法變更此選擇**。

- **授權模型**。 您可選擇的授權模型選項取決於您將擁有的部署類型。 您的識別提供者選項與租用戶虛擬機器、識別系統及其使用的帳戶無關。
    - [中斷連線部署](azure-stack-disconnected-deployment.md)中的客戶只有一個選項：以容量為基礎的計費。

    - [已連線部署](azure-stack-connected-deployment.md)中的客戶可以選擇以容量為基礎的計費或隨用隨付。 以容量為基礎的計費需有 Enterprise 合約 (EA) Azure 訂用帳戶才能註冊。 這是註冊的必要項目，因為這樣才能透過 Azure 訂用帳戶提供 Marketplace 中的項目可用性。

- **網路整合**。 [網路整合](azure-stack-network.md)是部署、操作及管理 Azure Stack 系統的重要項目。 有許多考量可用來確保 Azure Stack 解決方案具復原性，而且具有高可用性實體基礎結構來支援其作業。

- **防火牆整合**。 建議您[使用防火牆](azure-stack-firewall.md)來協助保護 Azure Stack 安全。 防火牆可以協助防止 DDOS 攻擊，並進行入侵偵測及內容檢查。 不過，請注意，這可能會成為 Azure 儲存體服務的輸送量瓶頸。


- **憑證需求**。 務必在現場工程師抵達您資料中心進行部署「之前」，準備好所有[所需憑證](azure-stack-pki-certs.md)。

一旦所有必要資訊都透過部署工作表收集好之後，解決方案提供者就會根據收集的資料啟動處理站程序，以確保將 Azure Stack 成功整合至您的資料中心。

## <a name="hardware-delivery-phase"></a>硬體寄送階段
當解決方案送達您的設施時，解決方案提供者會與您討論排程。 收到並安置好解決方案後，您需要與解決方案提供者排定時間，讓工程師到現場執行 Azure Stack 的部署。

**務必**在現場工程師抵達以部署解決方案之前，將所有必要資料鎖住並備妥。

-   必須購買及備妥所有憑證。

-   必須決定區域名稱。

-   所有網路整合參數都已完成，且符合您與解決方案提供者共用的項目。

> [!TIP]
> 如果任何一項資訊發生變更，請務必在排定實際部署之前，將此變更告知解決方案提供者。

## <a name="onsite-deployment-phase"></a>現場部署階段
若要部署 Azure Stack，您硬體解決方案提供者的現場工程師必須在場，才能開始進行部署。 為確保部署能成功，請確定所有透過部署工作表提供的資訊皆未曾變更。 

以下是部署體驗期間，現場工程師應執行的動作：

- 檢查所有纜線和邊界連線能力，以確保解決方案會正確地放在一起，並符合您的需求。
- 設定解決方案 HLH (硬體生命週期主機，如果存在)。
- 檢查並確定所有 BMC、BIOS 和網路設定皆正確。
- 確定所有元件的韌體是解決方案所核准的最新版本。
- 開始部署。

> [!NOTE]
> 由現場工程師進行的部署程序可能需要一個工作週來完成。

## <a name="post-deployment-phase"></a>部署後階段
在後置整合階段中，合作夥伴必須執行幾個步驟後，才能將解決方案會遞交給客戶。 在這個階段中，驗證是確保系統已正確部署和執行的重要部分。 

OEM 合作夥伴應採取的動作包括：

- [執行 test-azurestack](azure-stack-diagnostic-test.md#run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix)

- [向 Azure 註冊](azure-stack-registration.md)

- [Marketplace 摘要整合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- 備份交換器設定和 HLH 設定檔案

- 移除 DVM

- 準備用於部署的客戶摘要

- [檢查更新以確定解決方案軟體已更新為最新版本](./azure-stack-updates.md)

有幾個步驟會根據安裝類型來決定是必要或是選用。

- 如果部署是使用 [AD FS](azure-stack-integrate-identity.md) 來完成的，則 Azure Stack 戳記將需要與客戶自己的 AD FS 整合。

  > [!NOTE]
  > 此步驟由客戶負責，但合作夥伴可選擇性地提供可完成此作業的服務。

- 與個別合作夥伴的現有監視系統整合。

  -   [System Center Operations Manager 整合](azure-stack-integrate-monitor.md)也支援車隊管理功能。

  -   [Nagios 整合](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>整體時間軸

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支援
Azure Stack 可以在整個系統生命週期中啟用 Azure 一致的整合式支援體驗。 若要完全支援 Azure Stack 整合系統，客戶需要兩個支援合約；一個是 Microsoft (或其雲端解決方案提供者) 的 Azure 服務支援合約，一個是硬體提供者的系統支援合約。 整合的支援體驗會提供協調的升級和解決方案，讓客戶獲得一致的支援體驗 (無論誰先發出支援請求)。 若客戶已經有 Microsoft 的頂級支援、Azure - 標準/專業直接或合作夥伴支援，Azure Stack 軟體支援還是會包含在其中。

整合支援體驗會用到「案例交換」機制，以在 Microsoft 與硬體合作夥伴之間進行支援案例和案例更新的雙向傳輸。 Microsoft Azure Stack 會遵循[新式生命週期原則](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>後續步驟
深入了解[一般資料中心整合考量](azure-stack-datacenter-integration.md)。
