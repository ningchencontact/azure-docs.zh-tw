---
title: 使用 Azure 資料科學虛擬機器
description: 連接到 Azure 資料科學虛擬機器 (DSVM) 即可將可用的計算能力延伸到 Azure Notebook。
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: getroyer
ms.openlocfilehash: 9b762f1b3f1c17e15b051e72f5d2cf98bef446bf
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137762"
---
# <a name="use-azure-data-science-virtual-machines"></a>使用 Azure 資料科學虛擬機器

根據預設，專案則是在上執行**免費計算**層，也就是限制為 4 GB 的記憶體和 1 GB 的資料，以避免不當使用。 您可以藉由使用已佈建在 Azure 訂用帳戶中的其他虛擬機器來繞過這些限制。 基於此目的，最好的選擇是 Azure 資料科學虛擬機器 (DSVM) 使用**適用於 Linux (Ubuntu) 的資料科學虛擬機器**映像。 這類的 DSVM 已預先設定的所有內容，您需要 Azure notebook，並顯示自動開啟**執行**Azure Notebook 中的下拉式清單。

> [!Note]
> 只有在使用上的 Linux Ubuntu 映像建立的 Dsvm 上支援 azure Notebook。 在 Windows 2012、 Windows 2016 或 Linux CentOS 映像上不支援 notebook。

## <a name="create-a-dsvm-instance"></a>建立 DSVM 執行個體

若要建立新的 DSVM 執行個體，請依照[建立 Ubuntu 資料科學 VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro) 中的指示執行。 如需詳細資訊，包括定價詳細資料，請參閱 <<c0> [ 資料科學虛擬機器](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)。

## <a name="connect-to-the-dsvm"></a>連線至 DSVM

一旦您正在建立 DSVM，請選取**執行**Azure Notebook 上的下拉式清單專案儀表板，然後選取適當的 DSVM 執行個體。 下拉式清單顯示 DSVM 執行個體，是否下列條件成立：

- 您以使用 Azure Active Directory (AAD) 的帳戶 (如公司帳戶) 登入 Azure Notebooks。
- 您的帳戶已連接到 Azure 訂用帳戶。
- 您有一或多個虛擬機器，在該訂用帳戶，至少用於 Linux (Ubuntu) 映像中的資料科學虛擬機器的讀取存取。)

![專案儀表板上的下拉式清單中的資料科學虛擬機器執行個體](media/project-compute-tier-dsvm.png)

當您選取 DSVM 執行個體時，Azure Notebooks 可能會提示您輸入您在建立 VM 時使用的特定機器認證。

如果不符合任何一種情況，您仍可連線到 DSVM。 在下拉式清單中，選取**直接計算**選項，會提示您輸入的名稱 （顯示在清單中）、 VM 的 IP 位址和連接埠 (通常是 8000，以便 JupyterHub 接聽的預設連接埠) 和 VM 認證：

![收集 [直接計算] 選項伺服器資訊的提示](media/project-compute-tier-direct.png)

您從 Azure 入口網站中 DSVM 頁面取得這些值。

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>從 DSVM 存取 Azure Notebooks 檔案

若要保留的檔案路徑的同位檢查**免費計算**層，您就能夠只在 DSVM 上一次開啟一個專案。 若要開啟新的專案，您必須關閉開啟的專案第一次。

![Azure Notebook 中的 [關機] 按鈕](media/shutdown.png)

在 VM 上執行專案時，檔案都會掛接在根目錄中的 Jupyter 伺服器 （在 JupyterHub 中顯示的目錄），取代預設 Azure Notebooks 檔案。 當您關閉 VM 使用的位置**關機**notebook UI 中，Azure Notebooks 按鈕會還原預設檔案。

![Azure Notebook 中的 [關機] 按鈕](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>建立新的 DSVM 使用者

如果有多位使用者共用的 DSVM，您可以避免封鎖彼此建立並使用 DSVM 使用者針對每個 notebook 使用者：

1. 在  [Azure 入口網站](https://portal.azure.com)，瀏覽至您的虛擬機器。
1. 底下**支援與疑難排解**在左邊界中，選取**重設密碼**。
1. 輸入新的使用者名稱和密碼，然後選取**更新**。 （不影響現有的使用者名稱。）
1. 重複上一個步驟的任何其他使用者。

## <a name="next-steps"></a>後續步驟

深入了解 Dsvm 上[介紹 Azure 資料科學虛擬機器](/azure/machine-learning/data-science-virtual-machine/overview)。
