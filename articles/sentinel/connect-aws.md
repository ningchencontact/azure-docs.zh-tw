---
title: Symantec AWS 資料連接至 Azure 的 Sentinel 預覽 |Microsoft Docs
description: 了解如何將 Symantec AWS 資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673969"
---
# <a name="connect-azure-sentinel-to-aws"></a>Aws 連接 Azure 的 Sentinel

所有您的 AWS CloudTrail 事件串流到 Azure Sentinel 使用 AWS 連接器。 此連線程序會針對 Azure Sentinel 存取權限委派至您的 AWS 資源記錄，建立 AWS CloudTrail 和 Azure Sentinel 之間的信任關係。 這是建立以 Azure Sentinel 來存取您的 AWS 記錄檔中提供的權限的角色，就可完成 AWS 上。

## <a name="prerequisites"></a>必要條件

Azure Sentinel 工作區，您必須具有寫入權。

## <a name="connect-aws"></a>連線 AWS 
 
1.  在您的 Amazon Web Services 主控台中，下方**安全性、 身分識別與合規性**，按一下**IAM**。

    ![AWS1](./media/connect-aws/aws-1.png)

2.  選擇**角色**然後按一下**建立角色**。

    ![AWS2](./media/connect-aws/aws-2.png)

3.  選擇**另一個 AWS 帳戶。** 在 **帳戶識別碼**欄位中，輸入**的 Microsoft 帳戶識別碼**(**123412341234**) 可找到 AWS 連接器 Azure Sentinel 入口網站頁面中。

    ![AWS3](./media/connect-aws/aws-3.png)

4.  請確定**需要外部識別碼**已選取，然後輸入可以找到 AWS 連接器 Azure Sentinel 入口網站頁面中的外部識別碼 （工作區識別碼）。

    ![AWS4](./media/connect-aws/aws-4.png)

5.  底下**附加權限原則**選取**AWSCloudTrailReadOnlyAccess**。

    ![AWS5](./media/connect-aws/aws-5.png)

6.  輸入標記 （選擇性）。

    ![AWS6](./media/connect-aws/aws-6.png)

7.  然後，輸入**角色名稱**然後按一下**建立角色** 按鈕。

    ![AWS7](./media/connect-aws/aws-7.png)

8.  在 [角色] 清單中，選擇您所建立的角色。

    ![AWS8](./media/connect-aws/aws-8.png)

9.  複製**角色 ARN**將它貼到**角色，才能新增**Azure Sentinel 入口網站中的欄位。

    ![AWS9](./media/connect-aws/aws-9.png)

10. 若要在 Log Analytics 中用於 AWS 事件相關的結構描述，搜尋**AWSCloudTrail**。



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 AWS CloudTrail。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

