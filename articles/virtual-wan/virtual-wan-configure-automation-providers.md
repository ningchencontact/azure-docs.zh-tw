---
title: 設定 Azure 虛擬 WAN 自動化 - 適用於虛擬 WAN 合作夥伴 | Microsoft Docs
description: 本文協助軟體定義的連線解決方案合作夥伴來設定 Azure 虛擬 WAN 自動化。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918895"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>設定 Azure 虛擬 WAN 自動化 - 適用於虛擬 WAN 合作夥伴 (預覽)

本文協助您了解如何設定自動化環境，以連線和設定適用於 Azure 虛擬 WAN 的分支裝置 (客戶內部部署 VPN 裝置或 SDWAN)。 如果您是提供透過 IPsec/IKEv2 使用 VPN 連線的分支裝置提供者，則適合閱讀本文。

軟體定義的連線解決方案通常使用控制器或裝置佈建中心來管理其分支裝置。 控制器可以使用 Azure API，將 Azure 虛擬 WAN 的連線自動化。 此類型的連線需要位於內部部署的 SDWAN 或 VPN 裝置，且已對該裝置指派了對外開放的公用 IP 位址。

##  <a name="access"></a>存取控制

客戶必須能夠在裝置 UI 中針對虛擬 WAN 設定適當的存取控制。 建議使用 Azure 服務主體進行此設定。 服務主體型存取為裝置控制器提供適當的驗證，以上傳分支資訊。 如需詳細資訊，請參閱[建立服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)。

##  <a name="site"></a>上傳分支資訊

設計上傳分支 (內部部署網站) 資訊到 Azure 的使用者體驗。 **VPNSite** 的 [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) 可以用於在虛擬 WAN 中建立網站資訊。 您可以提供所有分支 SDWAN/VPN 裝置，或選取適當的裝置自訂。

##  <a name="hub"></a>中樞和服務

分支裝置上傳至 Azure 之後，客戶通常會在 Azure 入口網站中選取中樞區域和/或服務，如此會叫用一組作業來建立中樞虛擬網路和中樞內的 VPN 端點。 VPN 閘道是可調整規模的閘道，其大小是根據頻寬和連線需求來調整。

## <a name="device"></a>裝置設定

在此步驟中，未使用提供者的客戶將手動下載 Azure 設定，並將之套用到內部部署的 SDWAN/VPN 裝置。 身為提供者，您應該將這個步驟自動化。 控制器可以呼叫 **GetVpnConfiguration** REST API 來下載如下所示的 Azure 設定。

**設定注意事項**

  * 如果 Azure VNet 是附加到虛擬中樞，便會顯示為 ConnectedSubnets。
  * VPN 連線使用路由型設定和 IKEv2。

### <a name="understanding-the-device-configuration-file"></a>了解裝置設定檔

裝置設定檔包含了設定內部部署 VPN 裝置時所要使用的設定。 當您檢視此檔案時，請注意下列資訊：

* **vpnSiteConfiguration -** 此區段表示網站連線至虛擬 WAN 時設定的裝置詳細資料。 其中包含分支裝置的名稱和公用 IP 位址。
* **vpnSiteConnections -** 此區段提供下列相關資訊：

    * 虛擬中樞 VNet 的**位址空間**。<br>範例：
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 連線至中樞之 VNet 的**位址空間**。<br>範例：

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 虛擬中樞 Vpngateway 的 **IP 位址**。 由於 Vpngateway 的每個連線都由「主動對主動」設定中的 2 個通道組成，因此您會看到此檔案中列出這兩個 IP 位址。 在此範例中，您會看到每個網站的 "Instance0" 和 "Instance1"。<br>範例：

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway 連線設定詳細資料**，例如 BGP、預先共用金鑰等等。PSK 是自動為您產生的預先共用金鑰。 您隨時都可以在自訂 PSK 的 [概觀] 頁面中編輯連線。
  
### <a name="example-device-configuration-file"></a>範例裝置設定檔

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="default"></a>預設原則

### <a name="initiator"></a>啟動器

以下小節列出當 Azure 是通道的啟動器時，所支援的原則組合。

**第 1 階段**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2
* 3DES、SHA1、DH_GROUP_2
* 3DES、SHA_256、DH_GROUP_2

**第 2 階段**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* CBC_3DES、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* CBC_3DES、SHA_256、PFS_NONE


### <a name="responder"></a>回應者

以下小節列出當 Azure 是通道的回應者時，所支援的原則組合。

**第 1 階段**

* AES_256、SHA1、DH_GROUP_2
* AES_256、SHA_256、DH_GROUP_2
* AES_128、SHA1、DH_GROUP_2
* AES_128、SHA_256、DH_GROUP_2
* 3DES、SHA1、DH_GROUP_2
* 3DES、SHA_256、DH_GROUP_2

**第 2 階段**

* GCM_AES_256、GCM_AES_256、PFS_NONE
* AES_256、SHA_1、PFS_NONE
* CBC_3DES、SHA_1、PFS_NONE
* AES_256、SHA_256、PFS_NONE
* AES_128、SHA_1、PFS_NONE
* CBC_3DES、SHA_256、PFS_NONE
* CBC_DES、SHA_1、PFS_NONE 
* AES_256、SHA_1、PFS_1
* AES_256、SHA_1、PFS_2
* AES_256、SHA_1、PFS_14
* AES_128、SHA_1、PFS_1
* AES_128、SHA_1、PFS_2
* AES_128、SHA_1、PFS_14
* CBC_3DES、SHA_1、PFS_1
* CBC_3DES、SHA_1、PFS_2
* CBC_3DES、SHA_256、PFS_2
* AES_256、SHA_256、PFS_1
* AES_256、SHA_256、PFS_2
* AES_256、SHA_256、PFS_14
* AES_256、SHA_1、PFS_24
* AES_256、SHA_256、PFS_24
* AES_128、SHA_256、PFS_NONE
* AES_128、SHA_256、PFS_1
* AES_128、SHA_256、PFS_2
* AES_128、SHA_256、PFS_14
* CBC_3DES、SHA_1、PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>虛擬中樞 vpngateway 原則和我的內部部署 SDWAN/VPN 裝置或 SD-WAN 設定之間的所有項目都必須相符嗎？

您的內部部署 SDWAN/VPN 裝置或 SD-WAN 設定必須符合或包含以下您在 Azure IPsec/IKE 原則中指定的演算法和參數。 SA 存留期僅為本機規格，並不需要相符。

* IKE 加密演算法
* IKE 完整性演算法
* DH 群組
* IPsec 加密演算法
* IPsec 完整性演算法
* PFS 群組

## <a name="feedback"></a>預覽意見反應

歡迎提供意見反應。 若要回報任何問題，或針對虛擬 WAN 提供意見反應 (正面或負面皆可)，請寄送電子郵件至 <azurevirtualwan@microsoft.com>。 請在主旨列中以 "[]" 包含您的公司名稱。 如果您要回報問題，請也包含您的訂用帳戶 ID。

## <a name="next-steps"></a>後續步驟

如需虛擬 WAN 的詳細資訊，請參閱[關於 Azure 虛擬 WAN](virtual-wan-about.md) 和 [Azure 虛擬 WAN 常見問題集](virtual-wan-faq.md)。
