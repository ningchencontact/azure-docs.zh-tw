---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774220"
---
開始設定之前，請安裝並匯入必要的模組。 您需要系統管理員許可權，才能在 PowerShell 中安裝模組。

1. 安裝和匯入 Az 模組
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. 安裝和匯入 Az. 對等模組
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. 使用下列命令確認模組已匯入正常。
    ```powershell
    Get-Module
    ```
1. 使用下列命令登入您的 Azure 帳戶。
    ```powershell
    Connect-AzAccount
    ```
1. 檢查帳戶的訂閱，並選取您要在其中建立對等互連的訂用帳戶。
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. 如果您還沒有資源群組，您必須在建立對等互連之前建立一個。 您可以執行下列命令來這麼做：

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> 如果您尚未與您的 ASN 和訂用帳戶相關聯，請遵循[建立對等 ASN](../howto-subscription-association-powershell.md)關聯的步驟。 這是要求對等互連的必要參數。

> [!NOTE]
> 資源群組的位置與您選擇設定對等互連的位置無關。
&nbsp;
