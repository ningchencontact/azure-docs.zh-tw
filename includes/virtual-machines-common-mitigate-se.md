
**上次更新的文件**： 年 1 月 6，下午 6:30，太平洋標準時間。

最近公開[CPU 弱點的新類別](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)稱為推測執行側邊通道攻擊會導致從客戶尋求更清楚的問題。  

受保護的基礎結構，執行 Azure 並隔離開彼此客戶工作負載。  這表示在 Azure 上執行其他客戶無法攻擊您的應用程式使用這些弱點。

## <a name="keeping-your-operating-systems-up-to-date"></a>保持您的作業系統最新狀態

雖然作業系統更新不需要隔離您與 Azure 上執行其他客戶在 Azure 上執行的應用程式中，但一律是保持最新的作業系統版本的最佳作法。 

在下列供應項目中，以下是我們建議的動作來更新您的作業系統： 

<table>
<tr>
<th>供應項目</th> <th>建議的動作 </th>
</tr>
<tr>
<td>Azure 雲端服務 </td>  <td>啟用自動更新，或確定您執行最新的客體作業系統。</td>
</tr>
<tr>
<td>Azure 的 Linux 虛擬機器</td> <td>從您作業系統的提供者可用時安裝更新。 </td>
</tr>
<tr>
<td>Azure Windows 虛擬機器 </td> <td><ul><li>請確認您安裝作業系統更新之前，先執行支援的防毒應用程式。 相容性資訊，請連絡您的防毒軟體廠商。 </li> <li> 安裝[年 1 月的安全性彙總](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)。 </li></ul></td>
</tr>
<tr>
<td>其他 Azure PaaS 服務</td> <td>沒有使用這些服務的客戶所需的動作。 Azure 會自動保留您的作業系統版本最新狀態。 </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>如果您正在執行不受信任的程式碼的其他指引 

除非您執行不受信任的程式碼不需要採取任何其他客戶的動作。 如果您允許您不信任的程式碼 （例如，您允許您的客戶上傳二進位檔或程式碼片段，然後執行中雲端應用程式中的其中一個），則應該執行下列額外的步驟。  


### <a name="windows"></a>Windows 
如果您是使用 Windows，而且裝載未受信任的程式碼，您應該也會啟用名為核心虛擬位址 (KVA) 的遮蔽功能可提供額外的防護措施推測執行側邊通道弱點的 Windows 功能。 此功能依預設關閉，且可能會影響效能，如果已啟用。 請遵循[Windows 伺服器 KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)啟用保護的伺服器上的指示。 如果您執行 Azure 雲端服務，請確認您正在 WA-客體-OS-5.15_201801-01 或 WA-客體-OS-4.50_201801-01 （起可用在一月 10 日），並啟用登錄機碼透過啟動工作。


### <a name="linux"></a>Linux
如果您是使用 Linux 而且裝載未受信任的程式碼，您應該一併更新 Linux 實作核心分頁表隔離 (KPTI) 會從屬於使用者空間核心所使用的頁面資料表分隔它的較新版本。 這些防護功能需要的 Linux 作業系統更新，而且可以從您散發的提供者可用時取得。 您的作業系統提供者可以告訴您是否啟用或停用的預設保護。








## <a name="next-steps"></a>後續步驟

若要進一步了解，請參閱[CPU 的弱點可能會保護 Azure 客戶](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)。