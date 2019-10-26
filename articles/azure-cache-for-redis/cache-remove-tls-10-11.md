---
title: 透過 Azure Cache for Redis 移除使用 TLS 1.0 和 1.1 |Microsoft Docs
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
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901894"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>透過 Azure Cache for Redis 移除使用 TLS 1.0 和1。1

採用僅限使用 TLS 1.2 或更高版本的全產業推播。 TLS 版本1.0 和1.1 已知容易遭受攻擊，例如 BEAST 和貴賓，而且有其他常見的弱點和暴露（CVE）弱點。 它們也不支援 PCI 合規性標準所建議的新式加密方法和加密套件。 此[TLS 安全性 blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)會詳細說明其中的一些弱點。

雖然這些都不會造成立即的問題，但您應該考慮儘早使用 TLS 1.0 和1.1。 Azure Cache for Redis 從2020年3月31日起，將會停止支援這些 TLS 版本。 您的應用程式必須使用至少 TLS 1.2，才能在此日期之後與您的快取通訊。

本文提供有關如何從您的應用程式偵測和移除這些相依性的一般指引。

## <a name="check-if-your-application-is-already-compliant"></a>檢查您的應用程式是否已經符合規範

如果您的應用程式可以使用 TLS 1.2，最簡單的方式就是在它用來處理 TLS 1.2 的測試或暫存快取上設定最小的 TLS 版本。 您可以在 Azure 入口網站的快取實例的[Advanced 設定](cache-configure.md#advanced-settings)中，找到最低的 TLS 版本設定。 如果應用程式在這種變更之後仍繼續如預期般運作，則最有可能符合規範。 我們的應用程式所使用的某些 Redis 用戶端程式庫可能需要特別設定為啟用 TLS 1.2，才能透過該安全性通訊協定連接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>將您的應用程式設定為使用 TLS 1。2

大部分的應用程式會利用 Redis 用戶端程式庫來處理與其快取的通訊。 下列指示說明如何在各種程式設計語言和架構中設定一些熱門的用戶端程式庫，以使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

根據預設，Redis .NET 用戶端會使用最低的 TLS 版本（.NET Framework 4.5.2 或以下）和4.6 或更新版本上的最高 TLS 版本。 如果您使用較舊版本的 .NET Framework，您可以手動啟用 TLS 1.2：

* Stackexchange.redis. Redis：在連接字串中設定 `ssl=true` 和 `sslprotocls=tls12`。
* Servicestack.redis 時. Redis：遵循[這些指示](https://github.com/ServiceStack/ServiceStack.Redis/pull/247)。

### <a name="net-core"></a>.NET Core

根據預設，Redis .NET Core 用戶端會使用最高的 TLS 版本。

### <a name="java"></a>Java

Redis JAVA 用戶端在 JAVA 第6版或更低版本上使用 TLS 1.0。 如果快取上已停用 TLS 1.0，則 Jedis、萵苣和 Radisson 將無法連線至 Azure Cache for Redis。 目前沒有任何已知的因應措施。

在 JAVA 7 或以上版本中，Redis 用戶端預設不會使用 TLS 1.2，但可能會加以設定。 萵苣和 Radisson 目前不支援此許可權。 如果快取只接受 TLS 1.2 連線，則會中斷。 Jedis 可讓您使用下列程式碼片段來指定基礎 TLS 設定：

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

PHP 7 上的 Predis 將無法使用，因為後者僅支援 TLS 1.0。 在 PHP 7.2.1 或以下的情況下，Predis 預設會使用 TLS 1.0 或1.1。 當具現化用戶端時，您可以指定 TLS 1.2：

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
