---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9bd2ee708f7d27cff5d07ca7f86d925ca6d2741d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164717"
---
<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>進入維護模式
1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入] 。
2. 輸入密碼。 預設密碼為 **Password1**。
3. 在命令提示字元中，輸入：
   
     `Enter-HcsMaintenanceMode`
4. 您將會看到警告訊息，告知您維護模式將中斷所有 I/O 要求並提供與 Azure 傳統入口網站的連線，而系統將提示您進行確認。 輸入 **Y** 以進入維護模式。
   
    這兩個控制站都將重新啟動。 完成重新啟動時，會出現另一個訊息，指出裝置處於維護模式。

