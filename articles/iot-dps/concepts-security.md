---
title: "Azure IoT 中樞裝置佈建服務的安全性概念 | Microsoft Docs"
description: "說明具有裝置佈建服務和 IoT 中樞之裝置的特定安全性佈建概念"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: f004e4763106c25d94f585f644560cf3a72d3f1b
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2017
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT 中樞裝置佈建服務的安全性概念 

IoT 中樞裝置佈建服務是 IoT 中樞適用的協助程式服務，用於設定在指定 IoT 中樞上的全自動佈建裝置作業。 這項裝置佈建服務可以讓您以安全且可調整的方式佈建數百萬個裝置。 本文提供關於裝置佈建*安全性*概念的概觀。 本文與為裝置進行部署準備工作的所有角色相關。

## <a name="attestation-mechanism"></a>證明機制

證明機制是確認裝置身分識別的方法。 證明機制也與申請清單相關，該清單會讓佈建服務知道各裝置對應的證明方法。

> [!NOTE]
> IoT 中樞會針對該服務中的類似概念使用「驗證配置」。

裝置佈建服務支援兩種形式的證明：
* 以標準 X.509 憑證驗證流程為基礎的 **X.509 憑證**。
* 以針對金鑰使用 TPM 標準之 nonce 挑戰為基礎的 **SAS 權杖**。 使用此種形式時，裝置不需要有實體 TPM 即可執行此動作，但該服務會因為 [TPM 規格](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)的需求，而預期裝置使用簽署金鑰進行證明。

## <a name="hardware-security-module"></a>硬體安全模組

我們將硬體安全模組 (也稱為 HSM) 作為安全的硬體式裝置密碼儲存體使用，這是最安全的密碼儲存體。 X.509 憑證和 SAS 權杖都可以儲存在硬體安全模組 (HSM) 中。 硬體安全模組 (HSM) 可以和佈建支援的兩種證明機制搭配使用。

> [!TIP]
> 我們強烈建議裝置使用硬體安全模組 (HSM) 以在裝置上安全地儲存密碼。

您也可以將裝置密碼儲存在軟體 (記憶體) 上，但相較於硬體安全模組 (HSM)，該儲存方式比較不安全。

## <a name="trusted-platform-module-tpm"></a>信賴平台模組 (TPM)

TPM 可以參考安全地儲存驗證平台所用之金鑰的標準，以及與實作標準之模組互動的 I/O 介面。 TPM 可以是獨立硬體、整合式硬體、韌體或軟體。 深入了解 [TPM 和 TPM 證明](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)。 裝置佈建服務僅支援 TPM 2.0。

### <a name="endorsement-key"></a>簽署金鑰

簽署金鑰是在內部產生或插入在製造時間在 TPM 內所包含的非對稱金鑰，而且是唯一的每個 TPM。 您無法變更或移除簽署金鑰。 簽署金鑰的私密部分絕不會釋放到 TPM 外部，而簽署金鑰的公開部分則用於識別正版 TPM。 深入了解[簽署金鑰](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)。

### <a name="storage-root-key"></a>儲存根金鑰

儲存根金鑰儲存在 TPM 中，可保護應用程式建立的 TPM 金鑰，這樣一來，若沒有 TPM，就無法使用這些金鑰。 取得 TPM 擁有權後，會產生儲存根金鑰；而若您清除 TPM 以讓新的使用者取得擁有權，便又會產生新的儲存根金鑰。 深入了解[儲存根金鑰](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)。

## <a name="x509-certificates"></a>X.509 憑證

使用 X.509 憑證做為證明機制是生產環境調整規模並簡化裝置佈建的絕佳方式。 X.509 憑證通常會排列在信任鏈結中每個憑證由下一個較高的憑證，並依此類推，終止以自我簽署的根憑證的私密金鑰的憑證鏈結中。 這會建立委派從產生的受信任的根憑證授權單位 (CA) 向下透過每個裝置上安裝之終端實體憑證的中繼 CA 的根憑證的信任鏈結。 若要進一步了解，請參閱[使用 X.509 CA 憑證的裝置驗證](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview)。 

通常憑證鏈結代表某些與裝置相關聯的邏輯或實體階層架構。 例如，製造商可能會發行的自我簽署的根 CA 憑證，使用該憑證來產生每個處理站的唯一中繼 CA 憑證，請使用每個處理站的憑證，以產生每個實際執行的唯一中繼 CA 憑證工廠中一行和最後產生唯一的裝置 （終端實體） 憑證，每個裝置製造該行使用生產線的生產憑證。 若要進一步了解，請參閱[IoT 產業中的 X.509 CA 憑證的概念性的了解](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-concept)。 

### <a name="root-certificate"></a>根憑證

根憑證是自我簽署的 X.509 憑證，表示憑證授權單位 (CA)。 它是 terminus 或憑證鏈結的信賴起點。 根憑證可以自我組織所發出，或從受根憑證授權單位購買。 若要進一步了解，請參閱[取得 X.509 CA 憑證](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)。 根憑證可以也稱為根 CA 憑證。

### <a name="intermediate-certificate"></a>中繼憑證

中繼憑證是 X.509 憑證的已簽署的根憑證 （或另一個中繼憑證，以在其鏈結中的根憑證）。 鏈結中的最後一個中繼憑證會用來簽署分葉憑證。 中繼憑證可以也稱為中繼 CA 憑證。

### <a name="leaf-certificate"></a>分葉憑證

分葉憑證或終端實體憑證，請識別憑證持有者。 它有其憑證鏈結中的根憑證，以及零或多個中繼憑證。 分葉憑證無法用來簽署其他任何憑證。 唯一識別此裝置來佈建服務，且有時稱為裝置憑證。 在驗證期間，裝置會使用與此憑證相關聯的私密金鑰，證明持有挑戰回應來自服務。 若要進一步了解，請參閱[驗證裝置 X.509 CA 憑證以簽署](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)。

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>控制使用 X.509 憑證佈建服務的裝置存取

佈建的服務會公開兩種類型的註冊項目可讓您控制的使用 X.509 證明機制的裝置存取 exchange:  

- [個別註冊](./concepts-service.md#individual-enrollment)項目，皆具有特定裝置相關聯的裝置憑證。 這些項目會控制特定裝置的註冊。
- [註冊群組](./concepts-service.md#enrollment-group)項目是與特定的中繼或根 CA 憑證相關聯。 這些項目會控制所有裝置的中繼或根憑證其憑證鏈結中的註冊。 

當裝置連線到佈建服務時，服務會透過較不特定註冊項目指定優先順序更特定的註冊項目。 也就是說，如果存在個別註冊裝置，佈建服務適用於該項目。 如果沒有任何個別註冊裝置，裝置的憑證鏈結中的第一個中繼憑證的註冊群組存在，服務會套用該項目，依此類推到根目錄鏈結。 服務會套用它所找到第一個適用於項目，例如：

- 如果啟用第一個找到的註冊項目，則服務會佈建裝置。
- 第一個找到的註冊項目已停用，則服務不會不佈建裝置。  
- 如果找到任何裝置的憑證鏈結中的憑證沒有註冊的項目，則服務不會佈建裝置。 

這項機制，憑證鏈結的階層式結構提供功能強大具彈性的方式，您可以控制個別裝置以及與裝置群組的存取權。 例如，假設有五個裝置，使用下列憑證鏈結： 

- *裝置 1*： 根憑證]-> [A]-> [裝置 1 憑證的憑證
- *裝置 2*： 根憑證]-> [A]-> [裝置 2 憑證的憑證
- *裝置 3*： 根憑證]-> [A]-> [裝置 3 憑證的憑證
- *裝置 4*： 根憑證]-> [B]-> [裝置 4 憑證的憑證
- *裝置 5*： 根憑證]-> [B]-> [裝置 5 憑證的憑證

一開始，您可以建立根憑證，啟用所有五個裝置的存取權的單一已啟用的群組註冊項目。 如果憑證稍後 B 會成為洩露，您可以建立已停用的註冊群組項目，以避免憑證 B*裝置 4*和*裝置 5*無法註冊。 如果仍然稍後*裝置 3*變成入侵，您可以建立其憑證的已停用個別註冊項目。 這會撤銷存取權*裝置 3*，但是仍然允許*裝置 1*和*裝置 2*註冊。