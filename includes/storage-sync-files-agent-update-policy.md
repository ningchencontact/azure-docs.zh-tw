---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 80bb07d850628f07ebc37a39da1294399804d8f5
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164624"
---
Azure 檔案同步代理程式會定期更新，以新增功能和解決問題。 建議您設定 Microsoft Update，以取得 Azure 檔案同步代理程式的最新更新。

#### <a name="major-vs-minor-agent-versions"></a>主要與次要代理程式版本
* 主要代理程式版本通常會包含新功能，且會使用遞增的數字作為版本號碼的第一個部分。 例如：*2.\*.\**
* 次要代理程式版本也稱為「修補」，且會比主要版本更常發行。 它們通常包含錯誤修正和小幅改善，但是沒有任何新功能。 例如：*\*.3.\**

#### <a name="upgrade-paths"></a>升級路徑
有三個經核准及測試的方式可用來安裝 Azure 檔案同步代理程式更新。 這些更新路徑同時適用於主要和次要版本。
1. **(慣用) 設定 Microsoft Update，以自動下載並安裝代理程式更新。**  
    建議一律採用每個 Azure 檔案同步更新，以確保您可存取伺服器代理程式的最新修正程式。 Microsoft Update 會為您自動下載和安裝更新，讓這個程序順暢進行。
2. **使用 Microsoft Update 修補程式檔案或 .msp 可執行檔來修補現有的 Azure 檔案同步代理程式。您可以從 [Microsoft Update 目錄](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下載最新的 Azure 檔案同步更新套件。**  
    執行 .msp 可執行檔將會使用與 Microsoft Update 在先前升級路徑中自動使用的相同方法，來升級 Azure 檔案同步安裝。 套用 Microsoft Update 修補程式將會執行 Azure 檔案同步安裝的就地升級。
3. **從 [Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)下載最新的 Azure 檔案同步代理程式安裝程式。安裝程式下載是 Microsoft Installer 套件，或是 .msi 可執行檔。**  
    若要升級現有的 Azure 檔案同步代理程式安裝，請將舊版解除安裝，然後從所下載的安裝程式安裝最新版本。 Azure 檔案同步安裝程式會維護伺服器註冊、同步群組和任何其他設定。

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理程式生命週期和變更管理保證
Azure 檔案同步是一項雲端服務，能夠持續推出新的功能。 這表示特定的 Azure 檔案同步代理程式版本只會支援一段有限的時間。 為了簡化您的部署，下列規則保證您有足夠的時間和通知，可容納變更管理程序中的代理程式更新/升級：

- 主要代理程式版本從初始發行日期算起會獲得至少六個月的支援。
- 我們保證主要代理程式版本之間的支援至少有三個月的重疊。 
- 系統會在到期至少三個月之前，使用即將到期代理程式向已註冊的伺服器發出警告。 您可以在儲存體同步服務的已註冊伺服器區段之下，檢查已註冊的伺服器是否使用舊版的代理程式。
- 次要代理程式版本的存留期會繫結至相關聯的主要版本。 例如，當代理程式 3.0 版發行時，代理程式 2.\* 版全都會設定為一起過期。

> [!Note]
> 安裝具有到期警告的代理程式版本時會顯示警告，但仍會成功。 不支援嘗試安裝或與已過期的代理程式版本連線，而且會加以封鎖。