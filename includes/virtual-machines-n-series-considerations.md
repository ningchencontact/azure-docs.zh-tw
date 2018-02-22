## <a name="deployment-considerations"></a>部署考量

* 如需了解 N 系列 VM 的可用性，請參閱[依區域提供的產品](https://azure.microsoft.com/en-us/regions/services/)。

* N 系列 VM 只能部署在 Resource Manager 部署模型。

* N 系列 VM 對其磁碟支援的 Azure 儲存體類型有所不同。 NC 和 NV VM 只支援標準磁碟儲存體 (HDD) 所支持的 VM 磁碟。 NCv2、ND 和 NCv3 (預覽) VM 只支援進階磁碟儲存體 (SSD) 所支持的 VM 磁碟。

* 如果您想要部署不只一些 N 系列 VM ，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

* 您可能需要增加 Azure 訂用帳戶的核心配額 (依地區)，以及增加 NC、NCv2、ND 或 NV 核心的個別配額。 若要要求增加配額，可免費[開啟線上客戶支援要求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 預設限制會視您的訂用帳戶類別而有所不同。

* 您可以在 N 系列 VM 上部署的一個 VM 映像是 [Azure 資料科學虛擬機器](../articles/machine-learning/data-science-virtual-machine/overview.md)。 資料科學虛擬機器會預先安裝和設定許多常用的資料科學和深入學習工具。 它也會預先安裝 NVIDIA Tesla GPU 驅動程式。





