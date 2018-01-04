
GPU 最佳化 VM 大小是以單一或多個 NVIDIA Gpu 特製化的虛擬機器。 這些大小專為需要大量計算、 大量圖形和視覺效果的工作負載。 本文提供的數目和類型的 Gpu、 Vcpu、 資料磁碟和 Nic，以及此分組中的每種大小的儲存體輸送量和網路頻寬的相關資訊。 

* **NC、 NCv2 和 ND**大小適合需要大量計算和網路密集應用程式和演算法，包括基礎 CUDA 和 OpenCL 應用程式並模擬、 AI，深入學習。 
* **NV**大小最佳化，而且針對遠端的視覺效果、 資料流、 遊戲、 編碼和利用架構，例如 OpenGL 和 DirectX 的 VDI 案例所設計。  


## <a name="nc-instances"></a>NC 執行個體

由 NC 執行個體[NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf)卡。 使用者可以借助電腦透過更快速的資料，利用 CUDA 能源瀏覽應用程式、 損毀模擬、 光線追蹤的轉譯、 深入學習等等。 NC24r 組態可提供低延遲、 高輸送量網路介面針對緊密結合的平行運算工作負載最佳化。


| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 資料磁碟數目上限 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = 1/2 K80 卡。

*支援 RDMA

## <a name="ncv2-instances"></a>NCv2 執行個體

NCv2 執行個體是新一代的 NC 數列機器，由[NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) Gpu。 這些 Gpu 可以提供多個 2 x 目前 NC 數列計算效能。 客戶可以利用這些更新的 Gpu 傳統 HPC 工作負載，例如貯存槽模型、 DNA 排序、 蛋白質分析、 Monte Carlo 模擬，以及其他等等。 像是 NC 系列，NCv2 系列提供低延遲、 高輸送量網路介面針對緊密結合的平行運算工作負載最佳化，還設定。

> [!IMPORTANT]
> 對於此大小，您的訂用帳戶 vCPU （核心） 配額一開始設定為 0 在每個區域。 [要求增加配額 vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md)這個系列中[可用地區](https://azure.microsoft.com/regions/services/)。
>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 資料磁碟數目上限 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24r_v2 * |24 |1448 | 1344 | 4 | 32 |

1 GPU = P100 卡。

*支援 RDMA

## <a name="nd-instances"></a>ND 執行個體

ND 系列虛擬機器就會針對 AI 和深入學習工作負載所設計的 GPU 系列的新增。 它們提供了絕佳的效能訓練和推斷。 ND 執行個體由[NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu。 這些執行個體提供了絕佳的單精確度浮點運算，利用 Microsoft 認知工具組、 TensorFlow、 Caffe 及其他架構 AI 工作負載的效能。 ND 系列並提供更大的 GPU 記憶體大小 (24 GB)，而能夠用在更大的類神經網路模型。 像是 NC 系列，ND 系列提供次要的低延遲、 高輸送量網路 RDMA，透過組態，因此您可以執行大規模的訓練工作跨越許多 Gpu InfiniBand 連接。

> [!IMPORTANT]
> 對於此大小，每個區域，您的訂用帳戶中的 vCPU （核心） 配額一開始是設為 0。 [要求增加配額 vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md)這個系列中[可用地區](https://azure.microsoft.com/regions/services/)。
>

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 資料磁碟數目上限 |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6 |112 | 336 | 1 | 12 |
| Standard_ND12 |12 |224 | 672 | 2 | 24 |
| Standard_ND24 |24 |448 | 1344 | 4 | 32 |
| Standard_ND24r * |24 |1448 | 1344 | 4 | 32 |

1 GPU = P40 卡。

*支援 RDMA

## <a name="nv-instances"></a>NV 執行個體



NV 執行個體由[NVIDIA Tesla M60 ](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) Gpu 和 NVIDIA 格線桌面技術加速應用程式和虛擬桌面能夠以視覺化方式檢視其資料或模擬客戶所在地。 使用者能夠在 NV 執行個體上，將其圖形密集型工作流程視覺化以獲得較佳的圖形功能，此外還能夠執行單精確度工作負載，例如編碼和轉譯。 

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | GPU | 資料磁碟數目上限 |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = 1/2 M60 卡。


