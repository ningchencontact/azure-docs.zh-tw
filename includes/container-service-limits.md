| 資源 | 預設限制 |
| --- | :--- |
| 每個叢集的最大節點數 | 100 |
| 每個節點的最大 Pod 數 ([採用 Kubenet 的基本網路功能][basic-networking]) | 110 |
| 每個節點的最大 Pod 數 ([採用 Azure CNI 的進階網路功能][advanced-networking]) | 30<sup>1</sup> |
| 每個訂用帳戶的最大叢集數 | 20<sup>2</sup> |

<sup>1</sup> 此值可透過 ARM 範本部署加以自訂。 請參閱[此處的][arm-deployment-example]範例。<br />
<sup>2</sup> 建立 [Azure 支援要求][azure-support]可要求提高限制。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
