---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 57ba55ce284030a4103077553b0dcfce01a93678
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125746"
---
Azure 檔案同步代理程式會定期更新，以新增功能和解決問題。 建議您設定 Microsoft Update，以取得 Azure 檔案同步代理程式的最新更新。

#### <a name="major-vs-minor-agent-versions"></a>主要與次要代理程式版本
* 主要代理程式版本通常會包含新功能，且會使用遞增的數字作為版本號碼的第一個部分。 例如: \*2.\*.\*\*
* 次要代理程式版本也稱為「修補」，且會比主要版本更常發行。 它們通常包含錯誤修正和小幅改善，但是沒有任何新功能。 例如：\*\*.3.\*\*

#### <a name="upgrade-paths"></a>升級路徑
有四個經核准及測試的方式可用來安裝 Azure 檔案同步代理程式更新。 
1. **(慣用) 設定 Microsoft Update，以自動下載並安裝代理程式更新。**  
    建議一律採用每個 Azure 檔案同步更新，以確保您可存取伺服器代理程式的最新修正程式。 Microsoft Update 會為您自動下載和安裝更新，讓這個程序順暢進行。
2. **使用 AfsUpdater.exe 下載並安裝代理程式更新。**  
    AfsUpdater.exe 位於代理程式安裝目錄中。 按兩下可執行檔，即可下載並安裝代理程式的更新。 
3. **使用 Microsoft Update 修補程式檔案或 .msp 可執行檔來修補現有的 Azure 檔案同步代理程式。您可以從 [Microsoft Update 目錄](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下載最新的 Azure 檔案同步更新套件。**  
    執行 .msp 可執行檔將會使用與 Microsoft Update 在先前升級路徑中自動使用的相同方法，來升級 Azure 檔案同步安裝。 套用 Microsoft Update 修補程式將會執行 Azure 檔案同步安裝的就地升級。
4. **下載最新的 Azure 檔案同步代理程式安裝程式從[Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)。**  
    若要升級現有的 Azure 檔案同步代理程式安裝，請將舊版解除安裝，然後從所下載的安裝程式安裝最新版本。 Azure 檔案同步安裝程式會維護伺服器註冊、同步群組和任何其他設定。

#### <a name="automatic-agent-lifecycle-management"></a>自動代理程式生命週期管理
代理程式 」 第 6 版，檔案同步小組已引進了代理程式自動升級功能。 您可以選取其中一個模式，並指定伺服器上的維護期間，應嘗試升級。 這項功能可協助您藉由提供 guardrail，避免從過期的代理程式的代理程式的生命週期管理，或是以不費吹灰之力，保持目前設定。
1. **預設設定**會嘗試避免從過期的代理程式。 內的代理程式的已發佈的到期日的 21 天，該代理程式會嘗試自行升級。 就會開始升級每週一次之前到期，並在選取的維護期間的 21 天內嘗試。 **此選項不會不需要採取一般的 Microsoft Update 修補程式。**
2. （選擇性） 選取的代理程式將會自動升級本身為新的代理程式版本 （目前不適用於叢集伺服器）。 此更新將會發生在所選的維護期間，並允許您的伺服器，才會受益於新的功能和增強功能，只要它們已公開上市。 這是主要代理程式版本，以及定期更新修補程式到您的伺服器會提供建議、 煩惱設定。 每個發行的代理程式是 GA 品質。 如果您選取此選項時，Microsoft 將飛行的最新的代理程式版本給您。 叢集的伺服器會排除。 測試完成後，代理程式也會提供[Microsoft 下載中心](https://go.microsoft.com/fwlink/?linkid=858257)aka.ms/AFS/agent。

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理程式生命週期和變更管理保證
Azure 檔案同步處理是雲端服務，會持續介紹新功能和增強功能。 這表示特定的 Azure 檔案同步代理程式版本只會支援一段有限的時間。 若要簡化您的部署，下列規則保證您有足夠的時間和通知，可容納代理程式更新/升級您的變更管理程序：

- 主要代理程式版本從初始發行日期算起會獲得至少六個月的支援。
- 我們保證主要代理程式版本之間的支援至少有三個月的重疊。 
- 系統會在到期至少三個月之前，使用即將到期代理程式向已註冊的伺服器發出警告。 您可以在儲存體同步服務的已註冊伺服器區段之下，檢查已註冊的伺服器是否使用舊版的代理程式。
- 次要代理程式版本的存留期會繫結至相關聯的主要版本。 例如，當代理程式 3.0 版發行時，代理程式 2.\* 版全都會設定為一起過期。

> [!Note]
> 安裝具有到期警告的代理程式版本時會顯示警告，但仍會成功。 不支援嘗試安裝或與已過期的代理程式版本連線，而且會加以封鎖。
