---
title: 認識 SAP HANA on Azure (大型執行個體) 的詞彙 | Microsoft Docs
description: 認識 SAP HANA on Azure (大型執行個體) 的詞彙。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481032"
---
# <a name="know-the-terms"></a>認識詞彙

《架構和技術部署指南》中廣泛使用數個常見的定義。 請注意下列詞彙及其意義：

- **IaaS**:基礎結構即服務。
- **PaaS**:平台即服務。
- **SaaS**:軟體即服務。
- **SAP 元件**:個別 SAP 應用程式，例如 ERP Central Component (ECC)、 Business Warehouse (BW)、 Solution Manager 或企業版入口網站 (EP)。 SAP 元件可以傳統 ABAP 或 Java 技術為基礎，或以非 NetWeaver 應用程式 (例如商務物件) 為基礎。
- **SAP 環境**:一或多個 SAP 元件以邏輯方式分組執行像是開發、 品質保證、 訓練、 災害復原或生產環境的商務功能。
- **SAP 環境**:是指您 IT 架構中的整個 SAP 資產。 SAP 環境包含所有生產和非生產環境。
- **SAP 系統**:組合的 DBMS 層與應用程式層，例如 SAP ERP 開發系統，SAP BW 測試系統和 SAP CRM 生產系統。 Azure 部署不支援在內部部署與 Azure 之間分割這兩層。 SAP 系統可以在內部部署或在 Azure 中部署。 您可以將 SAP 環境的不同系統部署到 Azure 或內部部署中。 例如，您可以在 Azure 中部署 SAP CRM 開發和測試系統，而在內部部署 SAP CRM 生產系統。 就 SAP HANA on Azure (大型執行個體) 而言，您應在 VM 中裝載 SAP 系統的 SAP 應用程式層，並在 SAP HANA on Azure (大型執行個體) 戳記中的某個單位上裝載相關的 SAP HANA 執行個體。
- **大型執行個體 」 戳記**:為 SAP HANA TDI 認證並專門用來執行 Azure 內的 SAP HANA 執行個體的硬體基礎結構堆疊。
- **SAP HANA on Azure （大型執行個體）：** 供應項目在 Azure 中執行 SAP HANA TDI 認證的硬體部署在不同 Azure 區域中的大型執行個體戳記中的 HANA 執行個體的官方名稱。 相關詞彙 *HANA 大型執行個體*是 *SAP HANA on Azure (大型執行個體)* 的簡稱，廣泛使用於本技術部署指南中。
- **跨單位**:描述部署的案例，Vm 會具有站對站、 多站台或 Azure ExpressRoute 連線在內部部署資料中心與 Azure 之間的 Azure 訂用帳戶。 在一般 Azure 文件中，這類部署也會描述為跨單位案例。 之所以連線，是為了將內部部署網域、內部部署 Azure Active Directory/OpenLDAP 和內部部署 DNS 擴充到 Azure 中。 內部部署架構會擴充至 Azure 訂用帳戶的 Azure 資產。 透過此擴充，VM 可以是內部部署網域的一部分。 

   內部部署網域的網域使用者可以存取伺服器，並可在這些 VM 上執行服務 (例如 DBMS 服務)。 在內部部署的 VM 與 Azure 部署的 VM 之間可進行通訊和名稱解析。 這是部署大部分 SAP 資產時的典型案例。 如需詳細資訊，請參閱 < [Azure VPN 閘道](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)並[使用 Azure 入口網站建立具有站對站連線的虛擬網路](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- **Tenant**：部署於 HANA 大型執行個體 」 戳記的客戶回隔離到*租用戶。* 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 指派給不同租用戶的儲存體和計算單位無法看到彼此，也無法在 HANA 大型執行個體戳記層級上彼此通訊。 客戶可以選擇部署到不同的租用戶。 即使如此，租用戶彼此之間還是無法在 HANA 大型執行個體戳記層級上進行通訊。
- **SKU 類別**:對於 HANA 大型執行個體，提供下列兩種 Sku:
    - **類型 I 類別**:S72、 S72m、 S96、 S144、 S144m、 S192、 S192m 和 S192xm
    - **類型 II 類別**:S384、 S384m、 S384xm、 S384xxm、 S576m、 S576xm、 S768m、 S768xm 和 S960m


您可以透過其他多項資源了解如何在雲端中部署 SAP 工作負載。 如果您想要在 Azure 中執行 SAP HANA 的部署，您必須熟悉並了解 Azure IaaS 的主體及 Azure IaaS 上的 SAP 工作負載部署。 在繼續作業之前，請參閱[在 Azure 虛擬機器上使用 SAP 解決方案](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)以取得詳細資訊。 

**後續步驟**
- 請參閱 [HLI 認證](hana-certification.md)