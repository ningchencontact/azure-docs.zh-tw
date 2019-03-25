---
title: Microsoft Azure 資料箱閘道裝置的存取、電源和連線模式 | Microsoft Docs
description: 說明如何針對有助於將資料傳輸至 Azure 的 Azure 資料箱閘道裝置管理存取、電源和連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d46d74544181a6b9cbfd049b2f5461b20b928483
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401437"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>管理您 Azure 的 Data Gateway 的存取、 電源和連線模式

本文說明如何管理 Azure 資料箱閘道的存取、電源和連線模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。

在本文中，您將了解：

> [!div class="checklist"]
> * 管理裝置存取
> * 管理連線模式
> * 管理電源

## <a name="manage-device-access"></a>管理裝置存取

您的資料方塊閘道裝置的存取會受到使用裝置密碼。 您可以透過變更密碼的本機 web UI。 您也可以重設裝置密碼，在 Azure 入口網站中。

### <a name="change-device-password"></a>變更裝置密碼

遵循下列步驟，在本機的使用者介面，來變更裝置密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. 按一下 [變更密碼]。
 
### <a name="reset-device-password"></a>重設裝置密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]。

    ![重設密碼](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 按一下 [重設]。

    ![重設密碼](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設標準模式，您的裝置也可以在已部分中斷連線或已中斷連線的模式中執行。 每種模式的說明如下：

- **已部分中斷連線** – 在此模式中，裝置無法將任何資料上傳到共用，不過可透過 Azure 入口網站加以管理。

    此模式通常使用於計量付費的衛星網路，其目標在於減少網路頻寬使用量。 裝置監視作業仍可能需要最低網路使用量。

- **已中斷連線** – 在此模式中，裝置完全與雲端中斷連線，且雲端上傳和下載皆已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]。
2. 停用 [雲端上傳和下載連結]。
3. 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]。

    ![連線模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. 若要在已中斷連線的模式中執行裝置，請停用 [Azure 入口網站管理]。 裝置現在只能透過本機 Web UI 管理。

    ![連線模式](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>管理電源

您可以使用本機 Web UI 來關閉或重新啟動實體和虛擬裝置。 我們建議在重新開機之前，先讓主機上的共用離線，然後再讓裝置離線。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 視您想要進行的動作而定，按一下 [關機] 或 [重新啟動]。

    ![電源設定](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 系統提示您進行確認時，按一下 [是] 以繼續。

> [!NOTE]
> 如果您關閉虛擬裝置，則必須透過 Hypervisor 管理來啟動裝置。
