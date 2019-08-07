---
title: 使用 Azure Data Factory 將資料從您的 data lake 和資料倉儲遷移至 Azure |Microsoft Docs
description: 使用 Azure Data Factory 將資料從您的 data lake 和資料倉儲遷移至 Azure。
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: a61ddf57d4f01fcaf4a949add29c677aa94060a9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827853"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure 

當您想要將 data lake 或企業資料倉儲 (EDW) 遷移至 Azure 時, Azure Data Factory 可以是執行資料移轉的工具。 Data lake 遷移和資料倉儲遷移與下列案例相關: 

- 從 AWS S3、內部內部部署的 Hadoop 檔案系統到 Azure 的海量資料工作負載遷移。 
- EDW 從 Oracle Exadata、Netezza、Teradata、AWS Redshift 遷移至 Azure。 

Azure Data Factory 可以移動資料 lake 遷移的 PBs 資料, 以及數十 TB 資料來進行資料倉儲遷移。 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>為何 Azure Data Factory 可用於資料移轉 

- Azure Data Factory 可以輕鬆地相應增加能力, 以無伺服器的方式來移動資料, 其具有高效能、彈性和擴充性, 而且只需為您使用的部分付費。  
  - Azure Data Factory 對於資料量和檔案數目沒有任何限制。
  - Azure Data Factory 可以 100% 利用您的網路和儲存體頻寬, 在您的環境中達到最高的資料移動輸送量。   
  - Azure Data Factory 遵循「隨用隨付」策略, 因此您只需要支付使用 Azure Data Factory 來執行資料移轉至 Azure 的時間。  
- Azure Data Factory 能夠執行一次性的歷程記錄負載, 以及排程的累加式載入。 
- Azure Data Factory 使用 Azure IR 在可公開存取的 data lake/倉儲端點之間移動資料, 或使用自我裝載 IR 來移動 VNet 內部或防火牆後方之 data lake/倉儲端點的資料。 
- Azure Data Factory 具有企業級安全性: 使用 MSI 或服務身分識別進行安全的服務對服務整合, 或利用認證管理的 Azure Key Vault。 
- Azure Data Factory 提供無程式碼的撰寫體驗和豐富的內建監視儀表板。  

## <a name="online-vs-offline-data-migration"></a>線上與離線資料移轉

Azure Data Factory 是典型的線上資料移轉工具, 可透過網路 (網際網路、ER 或 VPN) 傳輸資料, 其中的離線資料移轉可讓使用者實際將資料傳輸裝置從您的組織寄送到 Azure 資料中心。  

當您選取 [線上] 與 [離線] 遷移方法時, 有三個重要的考慮:  

- 要遷移的資料大小。 
- 網路頻寬。 
- [遷移] 視窗。   

如果您想要在兩周內完成資料移轉 ([遷移] 視窗), 您可以在下圖中看到一條切割線, 以顯示何時適合使用具有不同資料大小和網路頻寬的線上遷移工具 (Azure Data Factory)。   

![線上與離線](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> 線上遷移方法的優點是, 您可以透過一項工具, 同時達成歷程記錄資料載入和累加式摘要。  如此一來, 您就可以在整個遷移視窗期間, 將資料保持在現有和新的存放區之間進行同步處理, 讓您可以在新的存放區上重建您的 ETL 邏輯並重新整理資料。 


## <a name="next-steps"></a>後續步驟

- [將資料從 AWS S3 遷移至 Azure](data-migration-guidance-s3-azure-storage.md)