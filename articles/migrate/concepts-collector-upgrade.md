---
title: 升級 Azure Migrate 中的收集器設備 | Microsoft Docs
description: 提供有關 Azure Migrate 收集器設備升級的資訊。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 88077ac965b2abb69be145f29cbadca2ff1128d6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836639"
---
# <a name="collector-update-release-history"></a>收集器更新版本歷程記錄

此文章摘要說明 [Azure Migrate](migrate-overview.md) 中收集器設備的升級資訊。

「Azure Migrate 收集器」是一個輕量型設備，可用來探索內部部署 vCenter 環境，目的是要在移轉至 Azure 之前先進行評量。 [深入了解](concepts-collector.md)。

## <a name="continuous-discovery-upgrade-versions"></a>連續探索：升級版本

目前針對連續探索設備尚未提供任何升級。

## <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>單次探索 (現在已被取代)：先前的升級版本

> [!NOTE]
> 一次性探索設備現在已被取代，因為這個方法依賴 vCenter Server 的統計資料設定來取得效能資料點可用性，而且收集到的平均效能計數器會導致 VM 大小不足，而無法遷移至 Azure。

### <a name="version-10916-released-on-10292018"></a>1.0.9.16 版 (2018 年 10 月 29 日發行)

包含設定設備時所面臨的 PowerCLI 問題修正。

升級[套件 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>1.0.9.14 版

升級[套件 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>1.0.9.13 版

升級[套件 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) 的雜湊值

**演算法** | **雜湊值**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="run-an-upgrade"></a>執行升級

您無須再次下載 OVA，即可將收集器升級至最新版本。

1. 您需下載以下清單中的最新升級套件。
2. 若要確保下載的 Hotfix 是安全的，您需開啟系統管理員命令視窗，然後執行下列命令來產生 ZIP 檔案的雜湊。 產生的雜湊應該符合針對特定版本所述的雜湊：

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    範例：**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. 將 ZIP 檔案複製到收集器設備 VM。
4. 在 ZIP 檔案上按一下滑鼠右鍵 > [解壓縮全部]。
5. 在 [Setup.ps1] 上按一下滑鼠右鍵 > [用 PowerShell 執行]，然後依照安裝指示進行操作。


## <a name="next-steps"></a>後續步驟

- [深入了解](concepts-collector.md)收集器設備。
- 為 VMware VM [執行評量](tutorial-assessment-vmware.md)。
