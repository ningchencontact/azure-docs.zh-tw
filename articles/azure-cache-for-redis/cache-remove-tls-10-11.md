---
title: 移除與 Azure Cache for Redis 搭配使用的 TLS 1.0 和 1.1 |Microsoft Docs
description: 瞭解如何在與 Azure Cache for Redis 通訊時，從應用程式移除 TLS 1.0 和1。1
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 638ce4b07201453d0bb00d3610cb695b72b6d698
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73806764"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>移除與 Azure Cache for Redis 搭配使用的 TLS 1.0 和1。1

針對傳輸層安全性（TLS）1.2 版或更新版本的專屬使用，有業界廣泛的推播。 TLS 版本1.0 和1.1 已知容易遭受攻擊，例如 BEAST 和貴賓，並具有其他常見的弱點和暴露（CVE）弱點。 它們也不支援支付卡產業（PCI）合規性標準所建議的新式加密方法和加密套件。 此[TLS 安全性 blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)會更詳細地說明其中一些弱點。

雖然這些考慮都不會造成立即的問題，但我們建議您儘快停止使用 TLS 1.0 和1.1。 Azure Cache for Redis 會在2020年3月31日停止支援這些 TLS 版本。 在該日期之後，您的應用程式就必須使用 TLS 1.2 或更新版本來與您的快取通訊。

本文提供有關如何偵測這些舊版 TLS 版本的相依性，並將其從您的應用程式中移除的一般指引。

## <a name="check-whether-your-application-is-already-compliant"></a>檢查您的應用程式是否已經符合規範

找出您的應用程式是否能與 TLS 1.2 搭配使用的最簡單方式，就是在其所使用的測試或暫存快取上，將**最小的 tls 版本**值設定為 tls 1.2。 **最低的 TLS 版本**設定位於 Azure 入口網站中快取實例的[Advanced 設定](cache-configure.md#advanced-settings)中。 如果應用程式在這種變更之後仍繼續如預期般運作，可能是相容的。 您可能需要特別設定一些應用程式所使用的 Redis 用戶端程式庫，以啟用 TLS 1.2，讓他們可以透過該安全性通訊協定連接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>將您的應用程式設定為使用 TLS 1。2

大部分的應用程式會使用 Redis 用戶端程式庫來處理與其快取的通訊。 以下指示說明如何在各種程式設計語言和架構中設定一些熱門的用戶端程式庫，以使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

Redis .NET 用戶端預設會在 .NET Framework 4.5.2 或更早版本上使用最舊的 TLS 版本，並使用 .NET Framework 4.6 或更新版本上的最新 TLS 版本。 如果您使用較舊版本的 .NET Framework，您可以手動啟用 TLS 1.2：

* **Stackexchange.redis. Redis：** 在連接字串中設定 `ssl=true` 和 `sslprotocls=tls12`。
* **Servicestack.redis 時. Redis：** 遵循[servicestack.redis 時. Redis 指示](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)。

### <a name="net-core"></a>.NET Core

Redis .NET Core 用戶端預設會使用最新的 TLS 版本。

### <a name="java"></a>Java

Redis JAVA 用戶端在 JAVA 第6版或更早版本上使用 TLS 1.0。 如果快取上已停用 TLS 1.0，Jedis、萵苣和 Radisson 將無法連線至 Azure Cache for Redis。 目前沒有任何已知的因應措施。

在 JAVA 7 或更新版本中，Redis 用戶端預設不會使用 TLS 1.2，但可以加以設定。 萵苣和 Radisson 目前不支援這種設定。 如果快取只接受 TLS 1.2 連線，它們就會中斷。 Jedis 可讓您使用下列程式碼片段來指定基礎 TLS 設定：

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Node Redis 和 IORedis 預設會使用 TLS 1.2。

### <a name="php"></a>PHP

Php 7 上的 Predis 將無法使用，因為 PHP 7 僅支援 TLS 1.0。 在 PHP 7.2.1 或更早版本上，Predis 預設會使用 TLS 1.0 或1.1。 當您建立用戶端實例時，可以指定 TLS 1.2：

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

在 PHP 7.3 或更新版本中，Predis 會使用最新的 TLS 版本。

對 phpredis 不支援任何 PHP 版本上的 TLS。

### <a name="python"></a>Python

Redis-.py 預設會使用 TLS 1.2。

### <a name="go"></a>GO

Redigo 預設會使用 TLS 1.2。

## <a name="additional-information"></a>其他資訊

- [如何設定 Azure Cache for Redis](cache-configure.md)
