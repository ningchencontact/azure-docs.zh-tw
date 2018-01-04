

SQL Server 或 Oracle 等某些資料庫工作負載需要大量記憶體、 存放裝置，和 I/O 頻寬，但不是高的核心計數。 許多資料庫工作負載不需要大量 CPU。 Azure 可讓您可以在此限制 VM vCPU 計數，以降低成本的軟體授權，同時維持相同的記憶體、 儲存和 I/O 頻寬特定 VM 大小。

VCPU 計數可以限制為原始的 VM 大小的一半或一季。 這些新的 VM 大小有指定，使其更容易地識別 active Vcpu 數目的尾碼。

例如，目前 VM 大小 Standard_GS5 隨附 32 Vcpu，448 GB 的 RAM，64 磁碟 (最多 256 TB)，和 80,000 IOPs 或 2 GB/s 的 I/O 頻寬。 新的 VM 大小 Standard_GS5 16 和 Standard_GS5 8 隨附 16 和 8 active Vcpu 分別維持記憶體、 儲存體和 I/O 頻寬 Standard_GS5 規格的其餘部分。

授權的費用支付 SQL Server 或 Oracle 受限為新的 vCPU 計數，以及其他產品扣款新 vCPU 計數為基礎。 這會導致 VM 規格的比例加大的 50%到 75%到作用中 （可計費） Vcpu。 這些新 VM 大小只可用在 Azure 中，讓工作負載，以 push 成本 （每個核心） 授權的花費更高的 CPU 使用率。 此時，計算成本，其中包括作業系統授權，會維持原始大小為同一個。 如需詳細資訊，請參閱[針對多個符合成本效益的資料庫工作負載的 Azure VM 大小](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)。


| 名稱                | vCPU | 規格           |
|---------------------|------|-----------------|
| Standard_M64 32ms   | 32   | M64ms 相同   |
| Standard_M64 16ms   | 16   | M64ms 相同   |
| Standard_M128 64ms  | 64   | M128ms 相同  |
| Standard_M128 32ms  | 32   | M128ms 相同  |
| Standard_E32 16_v3  | 16   | E32s_v3 相同 |
| Standard_E32 8s_v3  | 8    | E32s_v3 相同 |
| Standard_E64 32s_v3 | 32   | E64s_v3 相同 |
| Standard_E64 16s_v3 | 16   | E64s_v3 相同 |
| Standard_GS4 8      | 8    | GS4 相同     |
| Standard_GS4 4      | 4    | GS4 相同     |
| Standard_GS5 16     | 16   | GS5 相同     |
| Standard_GS5 8      | 8    | GS5 相同     |
| Standard_DS13 4_v2  | 4    | DS13_v2 相同 |
| Standard_DS13 2_v2  | 2    | DS13_v2 相同 |
| Standard_DS14 8_v2  | 8    | DS14_v2 相同 |
| Standard_DS14 4_v2  | 4    | DS14_v2 相同 |