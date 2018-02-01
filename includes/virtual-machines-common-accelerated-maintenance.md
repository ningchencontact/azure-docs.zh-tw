

## <a name="what-is-happening"></a>發生什麼事？

一項遍及整個業界、以硬體為基礎的安全性弱點於 [1 月 3 日洩漏](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)。 保護客戶安全一向是我們的最高指導原則，我們會採取主動作為，避免任何 Azure 客戶暴露在這些弱點衍生的風險中。

在安全性弱點公開揭露之後，我們[加快預定維護的時程](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)，並開始自動重新開機仍需要更新的虛擬機器。


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>如何查看哪些虛擬機器已更新？ 

您可以在 [Azure 入口網站的虛擬機器清單](https://aka.ms/T08tdc)中查看虛擬機器的狀態，以及是否完成重新開機。 如果已套用更新，虛擬機器會列為「已更新」，如果仍需更新則列為「已排程」。 如果只想要查看「已排程」虛擬機器，請參閱 [Azure 服務健康狀態](https://portal.azure.com/)。

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>如何得知虛擬機器重新開機的確切時間？

若要取得重新開機的相關警示，最佳方法是設定[排程事件](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events)。 這會在虛擬機器因維修而關閉的 15 分鐘前發出通知。

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>我可以在現在手動重新部署，藉此執行必要的維修作業嗎？ 

我們無法保證重新部署的虛擬機器會配置到更新過的主機。 Azure 網狀架構會盡可能將虛擬機器配置到已完成更新的主機。 然而，重新部署的虛擬機器可能會落在未更新的主機上。在這種情況下，您可能會因為排程維修而被迫進行第二次重新開機。 因此不建議採用手動重新部署來因應。

## <a name="how-long-will-the-reboot-take"></a>重新開機需要多久？ 

在大部分的情況下，重新開機需要花費約 **30 分鐘**的時間。

## <a name="does-the-guest-os-need-to-be-updated"></a>客體作業系統需要更新嗎？ 

此 Azure 基礎結構更新能在 Hypervisor 層級解決揭露的弱點，因此您的 Windows 或 Linux 虛擬機器映像不需要更新。 儘管如此，您還是應該像往常一樣，繼續對虛擬機器映像採用安全性最佳作法。 如有需要，請洽詢您的作業系統廠商以取得更新資訊和指示。 對於 Windows Server 虛擬機器客戶，現已發行指南，並在[這裡](../articles/virtual-machines/windows/mitigate-se.md)提供。

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>解決這項更新問題會造成效能影響嗎？

這項更新並未對大部分的 Azure 客戶產生明顯效能影響。 我們已最佳化 CPU 和磁碟 I/O 路徑，且在套用修正後並未發現明顯的效能影響。 少數客戶可能會受到一些網路效能影響。 使用 Azure 加速網路 (適用於 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 或 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)) 這項提供給所有 Azure 客戶的免費功能，即可處理此問題。

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>我採取了您的高可用性建議做法，我的環境在重新開機期間仍可維持高可用性嗎？

可以。部署在可用性設定組或虛擬機器擴展集的虛擬機器，具有更新網域 (UD) 建構。 執行維護時，Azure 會接受 UD 條件約束，並且不會從不同的 UD (在相同的可用性設定組內) 重新啟動虛擬機器。 如需高可用性的詳細資訊，請參閱[在 Azure 中管理 Windows 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或[在 Azure 中管理 Linux 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>我已使用區域配對設計商務持續性/災害復原計劃。 區域配對中的虛擬機器重新開機會發生在同一時間嗎？

一般而言，Azure 預定的維修事件一次會發行至配對區域一項，以將兩個區域的中斷風險降至最低。 不過，由於這項安全性更新十分緊急，我們會同時將更新推出到所有區域。

## <a name="what-about-paas-services-on-azure"></a>Azure 上的 PaaS 服務呢？  

包括網路和行動裝置、資料服務、IoT、無伺服器在內的多種 Azure 平台服務均已解決這項弱點。 使用這些服務的客戶無需採取任何行動。

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Intel 已在 2018 年 1 月 22 日發佈有關安全弱點的其他指示。  該指示會致使 Azure 進行任何額外的維修活動嗎？  

Azure 在 2018 年 1 月 3 日宣佈的風險降低措施，並未受到 Intel 發佈的[更新指示](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/)所影響。 我們不會因為該項新資訊而對客戶的虛擬機器實施額外的維修活動。
 

## <a name="next-steps"></a>後續步驟

若要進一步了解，請參閱[保護 Azure 客戶免於受到 CPU 漏洞攻擊](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)。
