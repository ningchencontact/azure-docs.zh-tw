---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 165a115f35810c1bfe463a571affb2e44ed74205
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893821"
---
### <a name="portal"></a>入口網站

如果您是第一次執行，則為您的磁片設定客戶管理的金鑰將會要求您以特定順序建立資源。 首先，您將需要建立並設定 Azure Key Vault。

#### <a name="setting-up-your-azure-key-vault"></a>設定您的 Azure Key Vault

1. 登入[Azure 入口網站](https://portal.azure.com/)並搜尋 Key Vault
1. 搜尋並選取 [**金鑰保存庫**]。

![sse-key-vault-portal-search .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)

> [!IMPORTANT]
> 您的 Azure 金鑰保存庫、磁片加密集、VM、磁片和快照必須全都位於相同的區域和訂用帳戶，部署才會成功。

1. 選取 [ **+ 新增**] 以建立新的 Key Vault。
1. 建立新的資源群組
1. 輸入金鑰保存庫名稱、選取區域，然後選取 [定價層]。
1. 選取 [審核] [ **+ 建立**]，驗證您的選擇，然後選取 [**建立**]。

![sse-create-a-key-vault .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. 完成部署金鑰保存庫後，請選取它。
1. 選取 [**設定**] 底下的 [**金鑰**]。
1. 選取**產生/匯入**

![sse-key-vault-generate-settings .png](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. 將 [**金鑰類型**] 設定為 [ **rsa** ]，並將 [ **rsa 金鑰大小**] 設定為**2080**。
1. 視需要填寫剩餘的選項，然後選取 [**建立**]。

![sse-create-a-key-generate .png](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>設定磁片加密集

若要建立及設定磁片加密集，您必須使用下列連結： https://aka.ms/diskencryptionsets 。 公用 Azure 入口網站中尚未提供磁片加密集建立。

1. 開啟 [[磁片加密集] 連結](https://aka.ms/diskencryptionsets)。
1. 選取 [+新增]。

![sse-create-disk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. 選取您的資源群組、命名您的加密集，然後選取與金鑰保存庫相同的區域。
1. 選取 [**金鑰保存庫] 和 [金鑰**]。
1. 選取您先前建立的金鑰保存庫和金鑰，以及版本。
1. 按 [選取]。
1. 選取 [審核] [ **+ 建立**]，然後按一下 [**建立**]。

![sse-disk-enc-set-blade-key .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. 完成建立後，請開啟磁片加密集，然後選取要顯示的警示。

![sse-disk-enc-alert-fix .png](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

兩個通知應該會快顯並成功。 這麼做可讓您搭配金鑰保存庫使用此集合。

![disk-enc-notification-success .png](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>部署 VM

現在您已建立並設定您的金鑰保存庫和磁片加密集，您可以使用加密來部署 VM。
VM 部署程式類似于標準部署程式，唯一的差異在於您需要在與其他資源相同的區域中部署 VM，而且您選擇使用客戶管理的金鑰。

1. 搜尋**虛擬機器**，然後選取 [ **+ 新增**] 以建立 VM。
1. 在 **基本** 索引標籤上，選取與您的磁片加密集相同的區域，然後 Azure Key Vault。
1. 視需要在 [**基本**] 索引標籤上填入其他值。

![sse-create-a-vm-region .png](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. 在 [**磁片**] 索引標籤上，選取 [待用**時使用客戶管理的金鑰進行加密**]。
1. 在 [**磁片加密集**] 下拉式選單中，選取您的磁片加密集。
1. 依您的需要進行其餘的選擇。

![sse-create-vm-select-cmk-encryption-set .png](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)