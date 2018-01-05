

## <a name="what-is-happening"></a>發生什麼事？

是一個業界、 以硬體為基礎的安全性弱點[洩漏月 3 日](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html)。 讓客戶安全一定是我們的首要而且，我們會執行使用中的步驟，以確保沒有任何 Azure 客戶會公開這些弱點。

使用公用洩漏的安全性弱點，我們[加速計劃的維護時間](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)又仍然需要更新的 Vm 時自動重新開機。
 
## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>如何查看哪些 Vm 已更新？ 

您可以查看您 Vm 的狀態，如果在重新開機完成的時間中[Azure 入口網站中的 VM 清單](https://aka.ms/T08tdc)。 如果更新已套用，或 「 排程 」 仍需要更新時，您的 Vm 將會列出為 「 已更新 」。 如果您想要查看只將 「 已排程 」 是指您 Vm 您[Azure 服務健全狀況](https://portal.azure.com/)。

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>找出完全當我的 Vm 將會重新啟動嗎？

若要取得相關的重新開機警示，最好是設定[排程的事件](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events)。 這提供 VM 的 15 分鐘通知因維護而停機 – 則會應用程式開發介面呼叫的 vm，此外，活動的記錄項目可用來觸發程序的 Azure 監視傳送電子郵件、 簡訊或 webhook。 不提供任何先前的通知。

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>我手動重新部署現在是用來執行所需的維護？ 

我們無法保證在重新部署的 VM 將會配置給更新的主機。 可能的情況下，Azure 網狀架構將會嘗試配置 Vm 已更新的主機。 有可能，重新部署 VM 無法進入非更新的主機上，在這種情況下您可能會受限於第二個重新開機，強制排程的維護的一部分。 如此一來，手動重新部署不會建議因應措施。

## <a name="how-long-will-the-reboot-take"></a>在重新開機大約需要多久？ 

大部分的重新開機花費的時間大約**30 分鐘**。

## <a name="does-the-guest-os-need-to-be-updated"></a>客體作業系統需要更新嗎？ 

此 Azure 基礎結構更新這項弱點揭發 hypervisor 層級，而且不需要更新您的 Windows 或 Linux VM 映像。 不過，那麼您應該繼續套用您的 VM 映像的安全性最佳作法。 請視需要參閱您的作業系統更新和指示，請廠商。 Windows Server VM 的客戶，指引現在已發行和使用。

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>都有可解決這項更新的結果影響效能嗎？

大部分的 Azure 客戶尚未看到顯著的效能影響，透過這項更新。 我們曾最佳化的 CPU 和磁碟 I/O 路徑，並套用修正後沒有看到顯著的效能影響。 少數客戶可能會遇到一些網路效能的影響。 這可以透過 Azure 加速網路，用於定址[Windows](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-powershell)或[Linux](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli)，這是提供給所有 Azure 客戶可用的功能。

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>我遵循您對於高可用性的建議，將我的環境維持高可用性重新啟動期間嗎？

[是]，部署可用性的虛擬機器設定，或虛擬機器擴展集將更新網域 (UD) 建構。 執行維護時，Azure 會接受 UD 條件約束，並且不會從不同的 UD (在相同的可用性設定組內) 重新啟動虛擬機器。 如需有關高可用性的詳細資訊，請參閱[管理 Windows Azure 中的虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或[管理 Azure 中 Linux 虛擬機器的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>我有架構使用區域組我商務持續性/災害復原計畫。 重新開機到我 Vm 會在區域組在相同的時間？

一般而言，Azure 計劃性的維護事件會發行至配對區域一次中斷這兩個地區的風險降至最低。 不過，由於這項安全性更新的緊急本質，我們會推出更新到所有地區同時。

## <a name="what-about-paas-services-on-azure"></a>在 Azure 上的 PaaS 服務呢？  

Azure 平台服務，包括 web 與行動裝置、 資料服務 IoT，無伺服器，等都解決弱點。 沒有使用這些服務的客戶所需的動作。

## <a name="next-steps"></a>後續步驟

若要進一步了解，請參閱[CPU 的弱點可能會保護 Azure 客戶](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)。