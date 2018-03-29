---
title: 為 Azure Stack 整合式系統部署準備 Azure Stack 公開金鑰基礎結構憑證 | Microsoft Docs
description: 說明如何準備 Azure Stack 整合式系統的 Azure Stack PKI 憑證。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: dadb443f8b7739e3a18c0d3beb558d8c42e9d19c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment"></a>準備 Azure Stack PKI 憑證以進行部署
必須使用與 Azure Stack 憑證需求符合的屬性，來匯入和匯出[取自您所選 CA 的](azure-stack-get-pki-certs.md)憑證檔案。


## <a name="prepare-certificates-for-deployment"></a>準備憑證以進行部署
使用下列步驟來準備及驗證 Azure Stack PKI 憑證： 

1.  將[取自您所選 CA 的](azure-stack-get-pki-certs.md)原始憑證版本複製到部署主機的目錄中。 
  > [!WARNING]
  > 如果已透過任何方式匯入、匯出或修改從 CA 直接提供的檔案，則請勿複製該檔案。

2.  將憑證匯入本機電腦憑證存放區中：

    a.  以滑鼠右鍵按一下憑證，選取 [安裝 PFX]。

    b.  在 [憑證匯入精靈] 中，選取 [本機電腦] 作為匯入位置。 選取 [下一步] 。

    ![本機電腦匯入位置](.\media\prepare-pki-certs\1.png)

    c.  在 [選擇要匯入的檔案] 頁面上選取 [下一步]。

    d.  在 [私密金鑰保護] 頁面上，輸入您憑證檔案的密碼，然後啟用 [將此索引鍵標示為可匯出。這可讓您在稍後備份或傳輸索引鍵] 選項。 選取 [下一步] 。

    ![將索引鍵標示為可匯出](.\media\prepare-pki-certs\2.png)

    e.  選擇 [將所有憑證放在下列存放區中]，然後選取 [企業信任] 作為位置。 按一下 [確定] 以關閉 [憑證存放區選取項目] 對話方塊，然後按 [下一步]。

    ![設定憑證存放區](.\media\prepare-pki-certs\3.png)

  f.    按一下 [完成] 以完成憑證匯入精靈。

  g.    針對您為部署提供的所有憑證重複此程序。

3. 以 Azure Stack 的需求將憑證匯出為 PFX 檔案格式：

  a.    開啟憑證管理員 MMC 主控台並連線到本機電腦憑證存放區。

  b.    移至 [企業信任] 目錄。

  c.    選取您在上述步驟 2 中匯入的其中一個憑證。

  d.    從 [憑證管理員] 主控台的工作列中，選取 [動作] > [所有工作] > [匯出]。

  e.    選取 [下一步] 。

  f.    選取 [是，匯出私密金鑰]，然後按 [下一步]。

  g.    在 [匯出檔案格式] 區段中，選取 [匯出所有延伸內容]，然後按 [下一步]。

  h.    選取 [密碼] 並提供憑證的密碼。 請記住這個密碼，因為它要作為部署參數使用。 選取 [下一步] 。

  i.    選擇要匯出之 pfx 檔案的檔案名稱和位置。 選取 [下一步] 。

  j.    選取 [完成]。

  k.    對於您在上述步驟 2 中針對部署匯入的所有憑證重複此程序。

## <a name="next-steps"></a>後續步驟
[驗證 PKI 憑證](validate-pki-certs.md)