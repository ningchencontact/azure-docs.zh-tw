---
title: 安裝適用於 Visual Studio 的 Azure 串流分析工具
description: 本文說明安裝需求，以及如何設定適用於 Visual Studio 的 Azure 串流分析工具。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762178"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安裝適用於 Visual Studio 的 Azure 串流分析工具
Azure 串流分析工具支援 Visual Studio 2017、2015 和 2013。 本文說明如何安裝和解除安裝這些工具。

如需使用工具的詳細資訊，請參閱[適用於 Visual Studio 的串流分析工具](stream-analytics-quick-create-vs.md)。

## <a name="install"></a>Install
### <a name="recommended-visual-studio-2019-and-2017"></a>建議使用：Visual Studio 2019 與 2017
* 下載 [Visual Studio 2019 (Preview 2 或更新版本) 與 Visual Studio 2017 (15.3 或更新版本)](https://www.visualstudio.com/)。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 不支援 Mac 版 Visual Studio 2017。 
* 串流分析工具在 Visual Studio 2017 中，屬於 **Azure 開發**與**資料儲存和處理**工作負載的一部分。 安裝 Visual Studio 時，請啟用其中一個工作負載。

啟用**資料儲存和處理**工作負載，如下所示：

![已選取資料儲存體和處理工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

啟用 **Azure 開發**工作負載，如下所示：

![已選取 Azure 開發工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* 在 [工具] 功能表中，選擇 [擴充功能和更新]。 在已安裝的擴充功能中尋找 Azure Data Lake 和 Stream Analytics 工具，然後按一下 [更新] 以安裝最新的擴充功能。 

![Visual Studio 的擴充功能和更新](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015、2013
* 安裝 Visual Studio 2015 或 Visual Studio 2013 Update 4。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 
* 使用 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 安裝 Microsoft Azure SDK for .NET 2.7.1 版或更新版本。
* 安裝 [Visual Studio 適用的 Azure 串流分析工具](https://www.microsoft.com/en-us/download/details.aspx?id=49504)。

## <a name="update"></a>更新

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 與 2017
新版本提醒會顯示在 Visual Studio 通知中。

![Visual Studio 的新版本提醒](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 與 2013
已安裝之 Visual Studio 適用的串流分析工具會自動檢查新版本。 請依照快顯視窗中的指示來安裝最新的版本。 


## <a name="uninstall"></a>解除安裝

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 與 2017
按兩下 Visual Studio 安裝程式，然後選取 [修改]。 從 [資料儲存和處理] 或 [Azure 開發] 工作負載取消核取 [Azure Data Lake 和串流分析工作] 核取方塊。

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 與 2013
移至控制台，並解除安裝 **Visual Studio 適用的 Microsoft Azure Data Lake 和串流分析工具**。





