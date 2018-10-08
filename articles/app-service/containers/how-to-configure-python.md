---
title: 在 Azure App Service 中設定內建 Python 映像
description: 本教學課程說明使用內建 Python 映像在 Azure App Service 上編寫和設定 Python 應用程式的選項。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228541"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>在 Azure App Service 中設定內建 Python 映像 (預覽)

本文說明如何在 [Linux 上的 App Service](app-service-linux-intro.md) 中設定內建映像，以執行您的 Python 應用程式。

## <a name="python-version"></a>Python 版本

在 Linux 上的 App Service 中，Python 執行階段會使用 `python-3.7.0` 版。

## <a name="supported-frameworks"></a>支援的架構

與 Web 伺服器閘道介面 (WSGI) 相容的 Web 架構版本只要與 `python-3.7` 執行階段相容，即受到支援。

## <a name="package-management"></a>封裝管理

在 Git 發佈期間，Kudu 引擎會在存放庫根目錄中尋找 [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files)，並自動使用 `pip` 在 Azure 中安裝套件。

若要在發佈之前產生此檔案，請瀏覽至存放庫根目錄，並在 Python 環境中執行下列命令：

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>設定您的 Python 應用程式

App Service 中的內建 Python 映像會使用 [Gunicorn](http://gunicorn.org/) 伺服器來執行您的 Python 應用程式。 Gunicorn 是適用於 UNIX 的 Python WSGI HTTP 伺服器。 App Service 會自動會自動為 Django 和 Flask 專案設定 Gunicorn。

### <a name="django-app"></a>Django 應用程式

如果您發佈包含 `wsgi.py` 模組的 Django 專案，Azure 會使用下列命令自動呼叫 Gunicorn：

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask 應用程式

如果您要發佈 Flask 應用程式，且進入點位於 `application.py` 或 `app.py` 模組中，則 Azure 會分別使用下列其中一個命令自動呼叫 Gunicorn：

```bash
gunicorn application:app
```

或 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>自訂啟動

若要定義應用程式的自訂進入點，請先使用自訂 Gunicorn 命令建立 _.txt_ 檔案，並將它放在專案的根目錄上。 例如，若要使用模組 _helloworld.py_ 和變數 `app` 啟動伺服器，請使用下列內容建立 _startup.txt_：

```bash
gunicorn helloworld:app
```

在 [應用程式設定](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 頁面中選擇 **Python|3.7** 作為 [執行階段堆疊]，並提供您在上一個步驟中建立的**啟動檔案**所具備的名稱。 例如 _startup.txt_。
