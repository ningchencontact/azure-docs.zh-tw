---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887195"
---
容器的設定是階層式的，主機電腦上的所有容器會使用共用的階層。

您可以使用下列其中一項來指定設定：

* [環境變數](#environment-variable-settings)
* [命令列引數](#command-line-argument-settings)

環境變數的值會覆寫命令列引數的值，並進一步覆寫容器映像的預設值。 如果您針對相同的組態設定，在環境變數和命令列引數中指定不同的值，則已具現化的容器將會使用環境變數中的值。

|優先順序|設定位置|
|--|--|
|1|環境變數| 
|2|命令列|
|3|容器映像預設值|

### <a name="environment-variable-settings"></a>環境變數設定

使用環境變數的好處包括：

* 可以設定多個設定。
* 多個容器可以使用相同的設定。

### <a name="command-line-argument-settings"></a>命令列引數設定

使用命令列引數的好處，在於每個容器可以使用不同的設定。
