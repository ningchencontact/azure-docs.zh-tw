---
title: "使用 Azure 備份針對 System Center Data Protection Manager 進行疑難排解 | Microsoft Docs"
description: "針對 System Center Data Protection Manager 的問題進行疑難排解。"
services: backup
documentationcenter: 
author: adigan
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 2244a39217f54eb5906d05990f19fc8ca2fdeb0e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>針對 System Center Data Protection Manager 疑難排解

您可以在[這裡](https://docs.microsoft.com/en-us/system-center/dpm/dpm-release-notes?view=sc-dpm-2016)找到 SC DPM 的最新版本資訊。
您可以在[這裡](https://docs.microsoft.com/en-us/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016)找到保護支援矩陣。

## <a name="replica-is-inconsistent"></a>複本不一致

基於各種原因可能發生此錯誤，例如複本建立作業失敗、變更日誌出現問題、磁碟區層級篩選點陣圖錯誤、來源電腦意外關閉、同步處理記錄溢位或複本實際不一致。 請依照下列步驟解決此問題：
- 若要移除不一致狀態，請手動執行一致性檢查或安排每日一致性檢查。
- 確定使用最新版的 MAB Server 或 System Center DPM
- 確定已啟用自動一致性檢查
- 嘗試從命令提示字元重新啟動服務 ("net stop dpmra" 後面接著 "net start dpmra")
- 確定符合網路連線和頻寬需求
- 檢查來源電腦是否意外關閉
- 確定磁碟狀況良好，且有足夠的空間提供給複本
- 確定沒有重複的備份作業同時執行

## <a name="online-recovery-point-creation-failed"></a>線上復原點建立失敗

請依照下列步驟解決此問題：
- 確定使用最新版的 Azure 備份代理程式
- 嘗試在保護工作區域中手動建立復原點
- 確定在資料來源上執行一致性檢查
- 確定符合網路連線和頻寬需求
- 複本資料處於不一致狀態。 建立此資料來源的磁碟復原點
- 確定複本存在且未遺漏
- 複本有足夠的空間可建立 USN 日誌

## <a name="unable-to-configure-protection"></a>無法設定保護

當 DPM 伺服器無法聯繫受保護的伺服器時會出現此錯誤。 請依照下列步驟解決此問題：
- 確定使用最新版的 Azure 備份代理程式
- 確定 DPM 伺服器與受保護伺服器之間有連線能力 (網路/防火牆/proxy)
- 如果您正在保護 SQL Server，請確定 NT AUTHORITY\SYSTEM 已從 [登入屬性] 啟用系統管理員 (sysadmin)

## <a name="this-server-is-not-registered-to-the-vault-specified-by-the-vault-credential"></a>此伺服器並未向保存庫認證所指定的保存庫未註冊

如果選取的保存庫認證檔案不屬於與所嘗試復原之 System Center DPM/Azure 備份伺服器關聯的復原服務保存庫，就會出現此錯誤。 請依照下列步驟解決此問題：
- 從已註冊 System Center DPM/Azure 備份伺服器的復原服務保存庫下載保存庫認證檔。
- 嘗試使用上次下載的保存庫認證檔，對保存庫註冊伺服器。

## <a name="either-the-recoverable-data-is-not-available-or-the-selected-server-is-not-a-dpm-server"></a>可復原資料無法使用，或選取的伺服器不是 DPM 伺服器
若沒有已向復原服務保存庫註冊的 System Center DPM/Azure 備份伺服器、伺服器尚未上傳中繼資料，或選取的伺服器不是 System Center DPM/Azure 備份伺服器時，會出現此錯誤。
- 如果有其他已向復原服務保存庫註冊的 System Center DPM/Azure 備份伺服器，請確定已安裝最新的 Azure 備份代理程式。
- 如果有其他 System Center DPM/Azure 備份伺服器已向復原服務保存庫註冊，請在安裝後等候一天，再開始復原程序。 夜間作業會將所有受保護備份的中繼資料上傳至雲端。 此資料可供復原。

## <a name="the-encryption-passphrase-provided-does-not-match-with-passphrase-associated-with-the-following-server"></a>提供的加密複雜密碼與下列伺服器關聯的複雜密碼不相符

> [!NOTE]
>如果您忘記/遺失加密複雜密碼，就沒有可復原資料的選項。 唯一的選項就是重新產生複雜密碼並用來加密未來的備份資料。
>
>

在加密來自要復原之 System Center DPM/Azure 備份伺服器的資料過程中使用的加密複雜密碼，與所提供的加密複雜密碼不符時，會出現此錯誤。 代理程式無法解密資料。 因此復原失敗。 請依照下列步驟解決此問題：
- 提供與要復原資料的 System Center DPM/Azure 備份伺服器關聯且完全相同的加密複雜密碼。 
