---
title: 開始使用 Azure 虛擬機器上的 TmaxSoft OpenFrame
description: 重新裝載您 IBM z/OS 主機的工作負載使用 TmaxSoft OpenFrame 環境在 Azure 虛擬機器 (Vm)。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896248"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>開始使用 Azure 上的 TmaxSoft OpenFrame

採用您現有的大型主機資產，並將它們移至 Microsoft Azure 中使用 TmaxSoft OpenFrame。 這個熱門的重新裝載方案建立模擬環境，在 Azure 上，讓您快速將應用程式移轉。 需要任何重新格式化。

## <a name="openframe-rehosting-environment"></a>OpenFrame 重新裝載的環境

設定 OpenFrame 環境在 Azure 上開發、 示範、 測試或生產環境工作負載。 下圖所示，OpenFrame 會包含在 Azure 建立的大型主機模擬環境的多個元件。 比方說，OpenFrame 線上服務來取代例如 IBM 客戶資訊控制系統 (CICS) 大型主機中介軟體。 OpenFrame 批次中，使用其 TJES 元件，會取代 IBM 大型主機的工作項目子系統 （只要）。 

![OpenFrame 重新裝載的處理序](media/openframe-01.png)

> [!NOTE]
> 若要在 Azure 上執行 OpenFrame 環境，您必須具有有效的產品授權或 TmaxSoft 從試用版授權。

## <a name="openframe-components"></a>OpenFrame 元件

下列元件是在 Azure 上的 OpenFrame 環境的一部分：

- **移轉工具**包括 OFMiner，解決方案會分析大型主機資產，然後移轉到 Azure。
- **編譯器**，包括 OFCOBOL，解譯在大型主機 COBOL 程式; 編譯器OFPLI，它會解譯大型主機的 PL / 我的程式;和 OFASM，解譯在大型主機組合器程式的編譯器。
- **前端**元件，包括 Java 企業使用者方案 (JEUS)、 Java Enterprise Edition 6.OFGW 經認證的 web 應用程式伺服器和 OpenFrame 閘道元件，可提供 3270 接聽程式。
- **應用程式**環境。 OpenFrame 基底是管理整個系統的中介軟體。 大型主機的中介軟體和 IBM CICS，會取代 OpenFrame 伺服器類型 C (OSC)。
- **關聯式資料庫**Tibero （顯示）、 Oracle 資料庫、 Microsoft SQL Server、 IBM Db2 或 MySQL 等。 OpenFrame 應用程式會使用開放式資料庫連接 (ODBC) 通訊協定，與資料庫通訊。
- **安全性**透過 TACF，控制使用者存取系統和資源服務模組。 
- **OFManager**是一個解決方案，可在 web 環境中的 OpenFrame 的作業和管理功能。

![OpenFrame 架構](media/openframe-02.png)

## <a name="next-steps"></a>後續步驟

- [在 Azure 上安裝 TmaxSoft OpenFrame](./install-openframe-azure.md)
