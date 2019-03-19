---
title: Microsoft Azure 資料方塊邊緣裝置的存取、 電源和連線模式 |Microsoft Docs
description: 說明如何管理存取、 電源及可協助將資料傳輸至 Azure 的 Azure 資料方塊的邊緣裝置的連線模式
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e0aa3468bda9f904d62e9e20545ac5f990cef521
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905290"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>適用於您的 Azure 資料方塊 Edge （預覽） 管理存取、 電源和連線模式

本文說明如何管理您的 Azure 資料方塊邊緣的存取、 電源和連線能力的模式。 這些作業都是透過本機 Web UI 或 Azure 入口網站執行。

在本文中，您將了解：

> [!div class="checklist"]
> * 管理裝置存取
> * 管理連線模式
> * 管理電源

> [!IMPORTANT]
> Data Box Edge 目前處於預覽狀態。 部署訂購並部署此解決方案之前，請檢閱 [Azure 預覽版使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="manage-device-access"></a>管理裝置存取

存取您的資料方塊的邊緣裝置會受到使用的裝置系統管理員密碼。 您可以透過本機 Web UI 變更系統管理員密碼。 您也可以在 Azure 入口網站中重設裝置管理員密碼。

### <a name="change-device-administrator-password"></a>變更裝置管理員密碼

請在本機 UI 中依照下列步驟變更裝置管理員密碼。

1. 在本機 Web UI 中，移至 [維護] > [密碼變更]。
2. 輸入目前的密碼，然後輸入新密碼。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 確認新的密碼。

    ![變更密碼](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 選取 [變更密碼] 。
 
### <a name="reset-device-administrator-password"></a>重設裝置管理員密碼

重設工作流程不需要使用者重新叫用舊密碼，在密碼遺失時很好用。 此工作流程會在 Azure 入口網站中執行。

1. 在 Azure 入口網站中，移至 [概觀] > [重設管理員密碼]。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 輸入新密碼並加以確認。 所提供的密碼必須介於 8 到 16 個字元。 此密碼必須有下列其中 3 種字元：大寫、小寫、數字和特殊字元。 選取 **重設**。

    ![重設密碼](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>管理連線模式

除了預設完全連線模式，也可以部分連線，或完全中斷連接模式中執行您的裝置。 每種模式的說明如下：

- **完全連接**-這是裝置會運作正常的預設模式。 在雲端上傳和下載的資料會在此模式中啟用。 您可以使用 Azure 入口網站或本機 web UI 來管理裝置。

- **已部分中斷連線** – 在此模式中，裝置無法將任何資料上傳到共用，不過可透過 Azure 入口網站加以管理。

    此模式通常使用於計量付費的衛星網路，其目標在於減少網路頻寬使用量。 裝置監視作業仍可能需要最低網路使用量。

- **已中斷連線** – 在此模式中，裝置完全與雲端中斷連線，且雲端上傳和下載皆已停用。 裝置只能透過本機 Web UI 管理。

    此模式通常使用於您要讓裝置離線時。

若要變更裝置模式，請遵循下列步驟：

1. 在裝置的本機 Web UI 中，移至 [組態] > [雲端設定]。
2. 從下拉式清單中，選取您想要在裝置運作的模式。 您可以從選取**完全相連**，**部分連線**，並**完全中斷**。 若要在已部分中斷連線的模式中執行裝置，請啟用 [Azure 入口網站管理]。

    ![連線模式](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理電源

您可以關閉或重新啟動您使用本機 web UI 中的實體裝置。 我們建議您重新啟動之前，讓共用離線，資料的伺服器，然後在裝置上。 此動作可讓資料損毀的可能性降至最低。

1. 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 選取 **關機**或是**重新啟動**取決於您想要進行。

    ![電源設定](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 當提示確認，請選取**是**繼續進行。

> [!NOTE]
> 如果您關閉實體裝置時，您必須按下電源按鈕將它開啟裝置上。

## <a name="next-steps"></a>後續步驟

- 了解如何[管理共用](data-box-edge-manage-shares.md)。