---
title: Azure Stack 記錄和資料處理 |Microsoft Docs
description: 深入了解 Azure Stack 如何收集資訊。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 7beb9dc31e8987d69c2cf9cf64be36caf0e0e308
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56319015"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack 記錄和客戶資料處理 
*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*  

由於 Microsoft 是 Azure Stack 相關個人資料的處理方或次要處理方，Microsoft 對所有客戶承諾，(a) [線上服務條款](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0)的「資料保護條款」小節之中的「個人資料的處理；GDPR」條款和 (b) [線上服務條款](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0)附件 4 的歐盟一般資料保護規定條款從 2018 年 5 月 25 日起生效。 

由於 Azure Stack 位於客戶資料中心，因此，對於透過[診斷](azure-stack-diagnostics.md)、[遙測](azure-stack-telemetry.md)和[計費](azure-stack-usage-reporting.md)而與 Microsoft 共用的資料，Microsoft 為唯一的資料控制方。  

## <a name="data-access-controls"></a>資料存取控制 
Microsoft 員工獲派調查特定的支援案例時，會獲得加密資料的唯讀存取權限。 Microsoft 員工也可以使用能在必要情況下刪除資料的工具。 對於客戶資料進行的所有存取行為都會受到稽核和記錄。  

資料存取控制：
1.  在案例結案後，資料最多保留 90 天。
2.  在 90 天期間內，客戶也可隨時選擇移除資料。
3.  Microsoft 員工獲得的存取權限視個別案例而定，且只有在需要協助解決支援問題時才會得到權限。 
4.  如果 Microsoft 必須與 OEM 合作夥伴共用客戶資料，必須經過客戶同意。  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>客戶有哪些資料主體要求 (DSR) 控制項？
如先前所述，Microsoft 會依客戶要求，提供刪除隨選資料的支援服務。 在資料永久刪除前，客戶可以隨時要求我們的支援工程師刪除特定案例的所有記錄。  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>刪除資料時，Microsoft 是否會通知客戶？
對於自動化資料刪除動作 (在案例關閉之後 90 天)，我們不會主動向客戶通知刪除。 

至於隨選資料刪除動作，Microsoft 支援工程師可取得工具進行隨選資料刪除，且可透過電話向客戶確認刪除完成。

## <a name="diagnostic-data"></a>診斷資料
在支援程序中，Azure Stack 操作員可以與 Azure Stack 支援和工程團隊[共用診斷記錄](azure-stack-diagnostics.md)，以利於進行疑難排解。

Microsoft 提供的工具和指令碼可供客戶收集和上傳要求的診斷記錄檔。 收集記錄檔之後，會透過 HTTPS 保護加密的連線傳送到 Microsoft。 由於 HTTPS 提供有線加密，因此傳輸中的加密不需要密碼。 收到記錄後，會將記錄加密並儲存起來，直到支援案例結案後 90 天自動刪除為止。

## <a name="telemetry-data"></a>遙測資料
[Azure Stack 遙測](azure-stack-telemetry.md)會透過「已連線使用者體驗」自動將系統資料上傳至 Microsoft。 Azure Stack 操作員有控制項可隨時自訂功能和隱私權設定。

Microsoft 不打算蒐集敏感性資料，例如信用卡號碼、使用者名稱和密碼、電子郵件地址或類似的敏感性資訊。 如果我們判斷不小心收到了敏感資訊，我們會予以刪除。 

## <a name="billing-data"></a>帳單資料
[Azure Stack 計費](azure-stack-usage-reporting.md)使用的是全球 Azure 的帳單及使用量管線，因此會遵循 Microsoft 合規性指導方針。

Azure Stack 操作員可以將 Azure Stack 設定為將使用量資訊轉送至 Azure 進行計費。 選擇隨用隨付計費模型的多節點 Azure Stack 客戶一定要這麼做。 使用量報告是經由遙測獨立進行控制，而選擇容量模式的多節點客戶或 Azure Stack 開發套件的使用者不需使用此功能。 若為上述案例，可以[使用註冊指令碼](azure-stack-usage-reporting.md)來關閉使用量報告。


## <a name="next-steps"></a>後續步驟 
[深入了解 Azure Stack 安全性](azure-stack-security-foundations.md) 
