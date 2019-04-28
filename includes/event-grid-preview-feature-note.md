---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561740"
---
這項功能處於預覽狀態。 若要使用這項功能，您必須安裝此模組的預覽擴充功能。

### <a name="install-extension-for-azure-cli"></a>安裝 Azure CLI 擴充功能

對於 Azure CLI，您需要[事件方格擴充功能](/cli/azure/azure-cli-extensions-list)。

在 [CloudShell](/azure/cloud-shell/quickstart) 中：

* 如果您先前已安裝過該擴充功能，請更新它 `az extension update -n eventgrid`
* 如果您先前尚未安裝過該擴充功能，請安裝它 `az extension add -n eventgrid`

針對本機安裝：

1. 在本機將 Azure CLI 解除安裝。
1. 安裝[最新版](/cli/azure/install-azure-cli)的 Azure CLI。
1. 啟動命令視窗。
1. 將舊版的擴充功能解除安裝 `az extension remove -n eventgrid`
1. 安裝擴充功能 `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>安裝 PowerShell 模組

對於 PowerShell，您需要 [AzureRM.EventGrid 模組](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)。

在 [CloudShell](/azure/cloud-shell/quickstart-powershell) 中：

* 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

針對本機安裝：

1. 以系統管理員身分開啟 PowerShell 主控台
1. 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

如果 `-AllowPrerelease` 參數無法使用，請使用下列步驟：

1. 執行 `Install-Module PowerShellGet -Force`
1. 執行 `Update-Module PowerShellGet`
1. 關閉 PowerShell 主控台
1. 以系統管理員身分重新啟動 PowerShell
1. 安裝模組 `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
