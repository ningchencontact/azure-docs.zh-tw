---
title: 使用 REST 介面的 「 azure 資料箱疑難排解 |Microsoft Docs
description: 描述如何透過 REST 介面的資料複製時，Azure 資料箱中看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: c5ceeb2e6419cab7945454087edd4c821db28343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204226"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>針對 Azure 資料方塊中的 Blob 儲存體相關的問題進行疑難排解

如何針對問題進行疑難排解此發行項詳細資料資訊可能會看到使用透過 REST 介面，在您的資料中的資料中的 Blob 儲存體複製資料時。 當您使用資料中的 Blob 儲存體與其他應用程式或用戶端程式庫，例如 Azure 儲存體總管、 AzCopy 或 Azure 儲存體程式庫適用於 Python 這些問題介面。

## <a name="errors-seen-in-azure-storage-explorer"></a>在 Azure 儲存體總管中看到的錯誤

本節將詳細說明一些資料方塊中的 Blob 儲存體中使用 Azure 儲存體總管時所面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|無法擷取子資源。 其中一個 HTTP 標頭的值不是正確的格式。|從**編輯**功能表上，選取**目標 Azure Stack Api**。 <br>重新啟動 Azure 儲存體總管。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |請檢查端點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`會新增至主機檔案，在此路徑： <li>`C:\Windows\System32\drivers\etc\hosts` 在 Windows，或 </li><li> `/etc/hosts` 在 Linux。</li>|
|無法擷取子資源。 <br>自我簽署的憑證的詳細資料： |您的裝置的 SSL 憑證匯入 Azure 儲存體總管： <li>從 Azure 入口網站下載的憑證。 如需詳細資訊，請移至[下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。</li><li>從**編輯**功能表上，選取**SSL 憑證**，然後選取**匯入憑證**。</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>AzCopy 為 Windows 所示的錯誤

本節將詳細說明一些資料方塊中的 Blob 儲存體中使用 AzCopy 的 Windows 時所面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|AzCopy 命令呈現懸置狀態一分鐘，再顯示此錯誤： <br>無法列舉目錄 https://... 無法解析遠端名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|請檢查端點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`新增至主機檔案位於： `C:\Windows\System32\drivers\etc\hosts`。|
|AzCopy 命令呈現懸置狀態一分鐘，再顯示此錯誤： <br>剖析來源位置時發生錯誤。 「 基礎連接已關閉：無法建立 SSL/TLS 安全通道的信任關係。|您的裝置的 SSL 憑證匯入系統的憑證存放區。 如需詳細資訊，請移至[下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。|


## <a name="errors-seen-in-azcopy-for-linux"></a>適用於 Linux 的 AzCopy 中看過的錯誤

本節將詳細說明一些適用於 Linux 的 AzCopy 使用資料中的 Blob 儲存體時所面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|AzCopy 命令呈現懸置狀態 20 分鐘，再顯示此錯誤： <br>剖析來源位置時發生錯誤`https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`。 沒有這類裝置或位址|請檢查端點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`新增至主機檔案位於： `/etc/hosts`。|
|AzCopy 命令呈現懸置狀態 20 分鐘，再顯示此錯誤： <br>剖析來源位置時發生錯誤... 無法建立 SSL 連線。|您的裝置的 SSL 憑證匯入系統的憑證存放區。 如需詳細資訊，請移至[下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>適用於 Python 的 Azure 儲存體文件庫中看過的錯誤

本節詳細說明一些當使用 Linux 用戶端進行資料複製時，於資料箱磁碟部署期間最常面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|其中一個 HTTP 標頭的值不是正確的格式。 |安裝的 Microsoft Azure 儲存體程式庫，適用於 Python 的版本不支援的資料 方塊中。 請參閱支援版本的 Azure 資料方塊中的 Blob 儲存體需求。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …|執行 Python 之前, 設定 REQUESTS_CA_BUNDLE 環境變數的 Base64 編碼的 SSL 憑證檔案的路徑 (請參閱如何[下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate))。 <br>例如:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>或者，將憑證新增至系統的憑證存放區，並再設定這個環境變數，該存放區的路徑。 <br> 例如，在 Ubuntu 上： <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>常見錯誤

這些錯誤不屬於任何應用程式。

|錯誤訊息  |建議的動作 |
|---------|---------|
|連接逾時。 |登入資料箱裝置，並檢查它解除鎖定。 任何一次裝置重新啟動，它保持鎖定，直到有人登入。|

## <a name="next-steps"></a>後續步驟

- 深入了解[資料方塊中的 Blob 儲存體系統需求](data-box-system-requirements-rest.md)。
