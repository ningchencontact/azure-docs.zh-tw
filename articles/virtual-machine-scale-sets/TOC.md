# [虛擬機器擴展集文件](index.yml)

# 概觀
## [什麼是虛擬機器擴展集？](overview.md)

# 快速入門
## [在 Azure 入口網站中建立](quick-create-portal.md)
## [使用 Azure CLI 2.0 建立](quick-create-cli.md)
## [使用 Azure PowerShell 建立](quick-create-powershell.md)
## 使用範本建立
### [Linux 擴展集](quick-create-template-linux.md)
### [Windows 擴展集](quick-create-template-windows.md)

# 教學課程
## 1 - 建立/管理擴展集
### [Azure CLI 2.0](tutorial-create-and-manage-cli.md)
### [Azure PowerShell](tutorial-create-and-manage-powershell.md)
## 2 - 使用資料磁碟
### [Azure CLI 2.0](tutorial-use-disks-cli.md)
### [Azure PowerShell](tutorial-use-disks-powershell.md)
## 3 - 使用自訂的 VM 映像
### [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
### [Azure PowerShell](tutorial-use-custom-image-powershell.md)
## 4 - 將應用程式部署到擴展集
### [Azure CLI 2.0](tutorial-install-apps-cli.md)
### [Azure PowerShell](tutorial-install-apps-powershell.md)
### [範本](tutorial-install-apps-template.md)
## 5 - 自動調整擴展集
### [Azure CLI 2.0](tutorial-autoscale-cli.md)
### [Azure PowerShell](tutorial-autoscale-powershell.md)
### [範本](tutorial-autoscale-template.md)
## 使用自訂 Packer 映像將應用程式部署至 Azure 虛擬機器擴展集
### [Azure CLI 2.0](https://docs.microsoft.com/learn/deploy-custom-vmss-app/index)

# 範例
## [Azure CLI 2.0](cli-samples.md)
## [PowerShell](powershell-samples.md)

# 作法

## 規劃和設計
### [設計考量](virtual-machine-scale-sets-design-overview.md)
### [了解執行個體識別碼](virtual-machine-scale-sets-instance-ids.md)

## 建立範本
### [了解擴展集範本](virtual-machine-scale-sets-mvss-start.md)
### [使用現有的虛擬網路](virtual-machine-scale-sets-mvss-existing-vnet.md)
### [使用自訂映像](virtual-machine-scale-sets-mvss-custom-image.md)
### [使用來賓型自動調整搭配 Linux 擴展集範本](virtual-machine-scale-sets-mvss-guest-based-autoscale-linux.md)

## 部署
### [使用 Visual Studio 建立](virtual-machine-scale-sets-vs-create.md)
### [使用可用性區域](virtual-machine-scale-sets-use-availability-zones.md)
### [自動調整擴展集](virtual-machine-scale-sets-autoscale-overview.md)
#### [使用 Azure 入口網站](virtual-machine-scale-sets-autoscale-portal.md)
#### [進階自動調整](../monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [應用程式擴展集](virtual-machine-scale-sets-deploy-app.md)
### [使用資料磁碟搭配擴展集](virtual-machine-scale-sets-attached-disks.md)
### 在擴展集中加密磁碟
#### [使用 PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md)
#### [使用 Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md)
### [使用大型擴展集](virtual-machine-scale-sets-placement-groups.md)
### [將擴展集範本轉換為使用受控磁碟](virtual-machine-scale-sets-convert-template-to-md.md)
### [使用低優先順序](virtual-machine-scale-sets-use-low-priority.md)

## 管理
### 一般管理工作
#### [使用 Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md)
#### [使用 Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md)
### [擴展集內的垂直縮放比例](virtual-machine-scale-sets-vertical-scale-reprovision.md)
### [作業系統自動升級](virtual-machine-scale-sets-automatic-upgrade.md)
### [修改擴展集](virtual-machine-scale-sets-upgrade-scale-set.md)
### [DSC 與擴展集](virtual-machine-scale-sets-dsc.md)
### [適用於擴展集的網路](virtual-machine-scale-sets-networking.md)
### [將範本轉換為受控磁碟](virtual-machine-scale-sets-convert-template-to-md.md)

## 疑難排解
### [Autoscale](virtual-machine-scale-sets-troubleshoot.md)

## 常見問題集
### [擴展集常見問題](virtual-machine-scale-sets-faq.md)

# 參考
## [Azure PowerShell](/powershell/azure/overview)
## [Azure CLI](../virtual-machines/azure-cli-arm-commands.md)
## [REST](/rest/api/virtualmachinescalesets/)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=compute)
## 價格 
### [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)
### [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-vm-scale-set)
