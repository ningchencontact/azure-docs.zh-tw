---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 50a215175d7305834a64b7e0cfbc153431b10b7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301738"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>在入口網站中檢視排定維護的 VM

一旦排定了計劃的一波維護，您就可以觀察受到即將到來之一波維護影響的虛擬機器清單。 

您可以使用 Azure 入口網站並尋找已排程維護的 VM。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側導覽中，按一下 [虛擬機器]。

3. 在 [虛擬機器] 窗格中，按一下 [資料行] 按鈕，開啟可用資料行的清單。

4. 選取並新增下列資料行︰

   **維護**：顯示 VM 的維護狀態。 以下是可能值：
      
      | Value | 描述 |
      |-------|-------------|
      | 立即開始 | VM 處於自助式維護期間，可讓您在這裡自行起始維護。 請參閱下述，以了解如何在您的 VM 上開始維護。 | 
      | 已排程 | 已排定 VM 進行維護，沒有選項供您起始維護。 您可以在此檢視中選取 [維護 - 排程] 期間或按一下虛擬機器，來了解維護維護期間。 | 
      | 已更新 | 已更新您的 VM，此時不需要採取任何進一步的動作。 | 
      | 稍後重試 | 您已啟動維護，但是沒有成功。 您可以在稍後使用自助式維護選項。 | 
      | 立即重試 | 您可以重試先前失敗的自行啟動維護。 | 
      | - | 您的 VM 不屬於計劃性維護的一部分。 |
      

   **維護 - 自助期間**：顯示您可以在 VM 上自行開始維護的時間範圍。
   
   **維護 - 排程期間**：顯示 Azure 會對您的 VM 進行維護，以便完成維護作業的時間範圍。 



## <a name="notification-and-alerts-in-the-portal"></a>入口網站中的通知和警示

Azure 會將電子郵件傳送至訂用帳戶擁有者和共同擁有者群組，來傳達計劃性維護排程。 您可以建立 Azure 活動記錄警示，將其他收件者和通道新增到這個通訊。 如需詳細資訊，請參閱[建立服務通知的活動記錄警示](../articles/azure-monitor/platform/alerts-activity-log-service-notifications.md)。

請務必將 [事件類型] 設定為 [計劃性維護]，並將 [服務] 設定為 [虛擬機器擴展集] 和/或 [虛擬機器]
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>從入口網站在 VM 上開始維護

查閱 VM 詳細資料時，您將能夠看到其他與維護相關的詳細資料。  
在 VM 詳細資料檢視的頂端，如果您的 VM 已包含在計劃的一波維護中，則將會新增通知功能區。 此外，還會新增選項以在可能時開始維護。 


按一下維護通知，以查看對計劃性維護具有其他詳細資料的維護頁面。 從這裡您將能夠在您的 VM 上**開始維護**。

一旦開始維護，將維護您的虛擬機器，而且維護狀態將在幾分鐘內更新以反映結果。

如果您錯過了自助服務期間，仍可以看到 Azure 將維護虛擬機器的期間。 
