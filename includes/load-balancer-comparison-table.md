---
title: 包含檔案
description: 包含檔案
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 7/26/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: fc98206b5940ce75ddf26cfd905b1e0a89e2d85c
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326239"
---
| | 標準 SKU | 基本 SKU |
| --- | --- | --- |
| 後端集區大小 | 支援最多 1000 個執行個體 | 支援最多 100 個執行個體 |
| 後端集區端點 | 在單一虛擬網路中的任何虛擬機器，包括虛擬機器混合、可用性設定組、虛擬機器擴展集。 | 在單一可用性設定組或虛擬機器擴展集中的虛擬機器。 |
| 可用性區域 | 在基本 SKU 中，輸入和輸出的區域備援和區域性前端、輸出流程對應存活區域失敗、跨區域負載平衡。 | n/a|
| 診斷 | Azure 監視器、多維度計量 (包括位元組和封包計數器)、健康情況探查狀態、連線嘗試 (TCP SYN)、輸出連線的健康情況 (SNAT 成功和失敗的流程)、使用中資料層測量 | 僅適用於公用 Load Balancer 的 Azure Log Analytics、SNAT 耗盡警示、後端集區健康情況計數。 |
| HA 連接埠 | 內部負載平衡器 | n/a |
| 預設保護 | 針對公用 IP 和 Load Balancer 端點的保護預設為關閉，必須使用網路安全性群組明確地將流程的流量加入允許清單。 | 預設為開啟，網路安全性群組為選擇性。 |
| [輸出連線](../articles/load-balancer/load-balancer-outbound-connections.md) | 多個可選擇退出個別負載平衡規則的前端。「必須」明確建立輸出案例，虛擬機器才能使用輸出連線能力。  不用輸出連線能力即可與「虛擬網路服務端點」連線，且不會計入已處理的資料。  必須透過輸出連線能力連線到任何公用 IP 位址 (包括無法作為 VNet 服務端點的 Azure PaaS 服務)，並計入已處理的資料。 只有內部 Load Balancer 在提供虛擬機器時，就無法透過預設 SNAT 進行輸出連線。 輸出 SNAT 的程式設計依傳輸通訊協定而異，以輸入負載平衡規則的通訊協定為依據。 | 單一前端，有多個前端時會隨機選取。  只有內部 Load Balancer 在提供虛擬機器時，會使用預設 SNAT。 |
| [多個前端](../articles/load-balancer/load-balancer-multivip-overview.md) | 輸入和[輸出](../articles/load-balancer/load-balancer-outbound-connections.md) | 僅輸入 |
| [健康狀態探查關閉行為](../articles/load-balancer/load-balancer-custom-probe-overview.md) | 執行個體探查關閉__和__所有探查關閉時，TCP 連線保持作用中。 | 執行個體探查關閉時，TCP 連線保持作用中。 所有探查關閉時，所有 TCP 連線都終止。 |
| 管理作業 | 大部分的作業 < 30 秒 | 通常是 60-90+ 秒。 |
| SLA | 99.99% (當資料路徑具有兩個狀況良好的虛擬機器時)。 | 內含在 VM SLA 中。 | 
| 價格 | 根據規則數目、與資源相關聯的輸入或輸出所處理的資料來計費。  | 不收費 |
|  |  |  |