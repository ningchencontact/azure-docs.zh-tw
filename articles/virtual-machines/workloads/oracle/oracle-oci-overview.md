---
title: 與 Oracle 雲端基礎結構整合 Microsoft Azure |Microsoft Docs
description: 深入了解整合 Oracle 資料庫在 Oracle 雲端基礎結構 (OCI) 與 Microsoft Azure 上執行的應用程式的解決方案。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 711fce9627537e68171c3b170121900d6b14ca1e
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743646"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle 應用程式解決方案，整合 Microsoft Azure 與 Oracle 雲端基礎結構 （預覽）

Microsoft 和 Oracle 已建立合作關係提供低延遲、 高輸送量跨雲端連線能力，可讓您利用最佳的兩個雲端。 

使用此跨雲端連線能力，您可以分割上 Oracle 雲端基礎結構 (OCI) 中，執行您的資料庫層的多層式應用程式和應用程式和 Microsoft Azure 上的其他層。 體驗是類似於在單一雲端中執行整個解決方案堆疊。 

> [!IMPORTANT]
> 此跨雲端功能目前為預覽狀態，以及一些[限制](#preview-limitations)。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

如果您想要部署完全在 Azure 基礎結構上的 Oracle 解決方案時，請參閱[Oracle VM 映像並將其部署在 Microsoft Azure 上的](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>案例概觀

跨雲端連線能力提供方案，讓您在此同時並享有在 OCI 託管的資料庫服務的優點的 Azure 虛擬機器上執行 Oracle 的業界領先應用程式和您自己自訂的應用程式。 

您可以在跨雲端組態中執行的應用程式包括：

* E-business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售應用程式
* Oracle Hyperion 財務管理

下圖是解決方案的已連線的高階概觀。 為了簡單起見，此圖表會顯示只有應用程式層和資料層。 根據應用程式 architecutre 中，您的解決方案可能包括在 Azure 中的其他層，例如 web 層。 如需詳細資訊，請參閱下列幾節。

![Azure OCI 解決方案概觀](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>預覽限制

* 在預覽中的跨雲端連線能力僅限於 Azure 東部美國 (eastus) 區域和 OCI 阿什本 (我們-阿什本-1) 區域。

## <a name="networking"></a>網路功能

企業客戶通常會選擇多樣化和部署工作負載，透過多個雲端的各種商務和操作上的原因。 若要擴展，客戶相互連接使用網際網路時，IPSec VPN 或使用雲端提供者的直接連線解決方案，其中包括您內部部署網路的雲端網路。 互連雲端網路，可以在時間、 金錢、 設計、 採購、 安裝、 測試和作業中需要投資向來不遺餘力。 

若要解決客戶面臨的考驗，Oracle 與 Microsoft 已啟用的整合式的多雲端體驗。 跨雲端網路功能藉由連接[ExpressRoute](../../../expressroute/expressroute-introduction.md)在 Microsoft Azure 中使用的線路[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) OCI 線路。 此連線是可能在鄰近性或與 OCI FastConnect 相同的對等互連位置中的 Azure ExpressRoute 對等互連位置的所在。 此設定可讓兩個雲端，而不需要中繼服務提供者之間的安全、 快速的連線。

使用 ExpressRoute 和 FastConnect，客戶可以對等互連虛擬網路在 Azure 中的透過 OCI，雲端虛擬網路提供，不會重疊的私人 IP 位址空間。 對等互連的兩個網路通訊 OCI 虛擬雲端網路中的資源，如同它們都在相同網路中的虛擬網路中允許的資源。

## <a name="network-security"></a>網路安全性

網路安全性是任何企業應用程式的重要元件，此多重雲端解決方案的核心。 任何流量透過 ExpressRoute 和 FastConnect 會傳遞透過私人網路。 此設定可讓 Azure 虛擬網路與 Oracle 雲端虛擬網路之間進行安全通訊。 您不需要提供在 Azure 中的任何虛擬機器的公用 IP 位址。 同樣地，您不需要在 OCI 的網際網路閘道。 所有的通訊是透過機器的私人 IP 位址。

此外，您可以設定[安全性清單](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)OCI 雲端虛擬網路和安全性規則 (連接至 Azure[網路安全性群組](../../../virtual-network/security-overview.md))。 您可以使用這些規則來控制虛擬網路中機器之間流動的流量。 在電腦層級，在子網路層級，以及在虛擬網路層級，您可以新增網路安全性規則。
 
## <a name="identity"></a>身分識別

身分識別是 Microsoft 與 Oracle 之間合作關係的核心支柱。 極為重要的工作已完成將整合[Oracle 的身分識別雲端服務](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)(IDCS) 與[Azure Active Directory](../../../active-directory/index.yml) (Azure AD)。 Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 它可協助您的使用者登入和存取各種資源。 Azure AD 也可讓您管理您的使用者和其權限。

目前，這項整合可讓您在單一中央位置，也就是 Azure Active Directory 中管理。 Azure AD 與對應的 Oracle 目錄同步處理目錄中的任何變更，並進行單一登入來跨雲端的 Oracle 解決方案。

## <a name="next-steps"></a>後續步驟

如需詳細資訊和相關 OCI 的白皮書，請參閱[Oracle 雲端](https://docs.cloud.oracle.com/iaas/Content/home.htm)文件。
