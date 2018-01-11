
**上次文件更新**：太平洋標準時間 1 月 6 日下午 6:30。

最近揭露的[新 CPU 漏洞類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)也稱為推測性執行旁路攻擊，讓許多客戶不知所措。  

執行 Azure 的基礎結構，以及將客戶的工作負載互相隔離這方面已受到保護。  這表示在 Azure 上執行的其他客戶無法使用這些漏洞來攻擊您的應用程式。

## <a name="keeping-your-operating-systems-up-to-date"></a>讓您的作業系統維持最新狀態

雖然無需更新作業系統，就能隔離您在 Azure 上執行的應用程式與其他在 Azure 上執行的客戶，但最佳做法是讓作業系統版本維持在最新狀態。 

在下列供應項目中，以下是我們對更新作業系統提供的建議的動作： 

<table>
<tr>
<th>供應項目</th> <th>建議的動作 </th>
</tr>
<tr>
<td>Azure 雲端服務 </td>  <td>啟用自動更新，或確定您執行最新的客體作業系統。</td>
</tr>
<tr>
<td>Azure Linux 虛擬機器</td> <td>從您作業系統提供者安裝更新 (若可用)。 </td>
</tr>
<tr>
<td>Azure Windows 虛擬機器 </td> <td>請確認您執行的是支援的防毒應用程式，再安裝作業系統更新。 請連絡您的防毒軟體廠商以取得相容性資訊。<p> 安裝 [1 月份的安全性彙總](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)。 </p></td>
</tr>
<tr>
<td>其他 Azure PaaS 服務</td> <td>使用這些服務的客戶無需採取任何行動。 Azure 會自動將您的作業系統版本保持在最新狀態。 </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>如果您執行的是不受信任的程式碼的其他指引 

除非您執行的是不受信任的程式碼，否則客戶無需採取任何其他行動。 如果您允許不信任的程式碼 (例如，您允許您的其中一位客戶之上傳二進位檔或程式碼片段，然後在雲端的應用程式中執行)，則應該進行以下的其他步驟。  


### <a name="windows"></a>Windows 
如果您是使用 Windows，而且裝載未受信任的程式碼，您應該也會啟用名為核心虛擬位址 (KVA) 的遮蔽功能；此功能可針對推測性執行旁路攻擊漏洞提供額外的防護措施。 依預設此功能為關閉，若啟用可能會影響效能。 請遵循 [Windows 伺服器 KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) 啟用伺服器保護中的指示。 如果您執行的是 Azure 雲端服務，請確認您正在執行 WA-GUEST-OS-5.15_201801-01 或 WA-GUEST-OS-4.50_201801-01 (於 1 月 10 日起可用) 並透過啟動工作啟用登錄機碼。


### <a name="linux"></a>Linux
如果您使用的是 Linux 而且裝載未受信任的程式碼，您應該將 Linux 一併更新為最新版本，以實作核心分頁表隔離 (KPTI)，從屬於使用者空間的分頁表中分隔出核心使用的分頁表。 這些防護功能需要進行 Linux 作業系統更新，並可以在可用時從散發提供者處取得這些功能。 您的作業系統提供者可以告訴您依預設，保護是否已啟用或停用。








## <a name="next-steps"></a>後續步驟

若要進一步了解，請參閱[保護 Azure 客戶免於受到 CPU 漏洞攻擊](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)。