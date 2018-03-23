---
title: Azure VM 上的 SAP HANA 可用性 - 概觀 | Microsoft Docs
description: Azure 原生 VM 上的 SAP Hana 作業
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b22f89750234a4715d2b7fd2df6ad8740b1d085
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-high-availability-guide-for-azure-virtual-machines"></a>Azure 虛擬機器的 SAP HANA 高可用性指南
Azure 提供許多功能，可以在 Azure VM 中部署像是 SAP HANA 的任務關鍵性資料庫。 本文件提供的指引會引導您達成 Azure 虛擬機器中裝載之 SAP HANA 執行個體的可用性。 文件中描述可以在 Azure 基礎結構上實作以提高 Azure 上 SAP HANA 可用性的幾個案例。 

## <a name="prerequisites"></a>先決條件
本指南假設您已熟悉 Azure 上的這類基礎結構即服務 (IaaS) 基本知識： 

- 如何透過 Azure 入口網站或 PowerShell 部署虛擬機器或虛擬網路。
- Azure 跨平台命令列介面 (CLI)，包括使用 JavaScript 物件通知 (JSON) 範本的選項。

本指南也假設您已熟悉安裝 SAP HANA 執行個體和管理及操作 SAP HANA 執行個體。 特別是與 HANA 系統複寫或像是備份和還原 SAP HANA 資料庫等工作相關的設定和作業。

在 Azure 中提供 SAP HANA 主題良好概觀的其他文章如下：

- [在 Azure VM 上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 虛擬機器中設定 SAP HANA 系統複寫](sap-hana-high-availability.md)
- [Azure 虛擬機器上的 SAP HANA 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)

您應該熟悉 SAP HANA 的相關文章和內容如下：

- [SAP HANA 的高可用性](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [常見問題集：SAP HANA 的高可用性](https://archive.sap.com/documents/docs/DOC-66702)
- [如何執行 SAP HANA 的系統複寫](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2.0 SPS 01 新增功能：高可用性](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [SAP HANA 系統複寫的網路建議](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [SAP HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [SAP HANA 服務自動重新啟動](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [設定 SAP HANA 系統複寫](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)


除了熟悉在 Azure 中部署 VM 以外，我們也建議先閱讀[在 Azure 中管理 Windows 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)一文，再繼續於 Azure 中定義可用性架構。

## <a name="service-level-agreements-for-different-azure-components"></a>適用於不同 Azure 元件的服務等級協定
Azure 對不同的元件 (例如網路、儲存體和 VM) 有不同的可用性 SLA。 這些 SLA 都已記載並可從 [Microsoft Azure 服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面開始尋找。 如果您查看 [虛擬機器的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/)，您就會了解 Azure 提供具有兩個不同組態的兩個不同 SLA。 SLA 定義如下：

- 對 OS 磁碟和所有資料磁碟使用 [Azure 進階儲存體](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)的單一 VM 可提供每月 99.9% 的運作時間
- 在 [Azure 可用性設定組](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中組織的多個 (至少兩個) VM 可提供每月 99.95% 的運作時間

根據 Azure 元件可以提供的 SLA 測量您的可用性需求，然後決定您需要以 SAP HANA 實作的不同案例，以達成您要求提供的可用性。

## <a name="next-steps"></a>後續步驟
繼續閱讀以下文件：

- [單一 Azure 區域中的 SAP HANA 可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 















  


