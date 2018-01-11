---
title: "管理裝置註冊使用 Azure 裝置佈建服務 Sdk |Microsoft 文件"
description: "如何管理在 IoT 中樞裝置佈建服務服務 Sdk 與裝置註冊"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 82da49924e71a38ca557f244f2830e1da45826b1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>如何管理 Azure 裝置佈建服務 Sdk 與裝置註冊
A*裝置註冊*建立單一裝置或可能在某個時間點向裝置佈建服務註冊的裝置群組的記錄。 註冊記錄包含屬於該註冊一部分之裝置一開始所需的設定，包括所需的 IoT 中樞。 本文示範如何管理裝置註冊使用 Azure IoT 佈建服務 Sdk，以程式設計方式佈建服務。  使用中 Azure IoT Sdk 為相同的儲存機制的 GitHub 上的 Sdk。

## <a name="samples"></a>範例
本文章會檢閱來管理裝置註冊使用 Azure IoT 佈建服務 Sdk，以程式設計方式佈建服務的高層級的概念。  確切的 API 呼叫可能因語言不同而不同。  請檢視我們在 GitHub 提供如需詳細資訊的範例：
* [Java 佈建服務用戶端範例](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [Node.js 佈建服務用戶端範例](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>必要條件
* 從裝置佈建服務執行個體的連接字串
* 裝置安全性成品：
    * [**TPM**](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security):
        * 個別的註冊： 註冊 ID 和 TPM 簽署金鑰從實體裝置或 TPM 模擬器。
        * 註冊群組不適用於 TPM 證明。
    * [**X.509**](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security):
        * 個別的註冊：[分葉憑證](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#leaf-certificate)從實體裝置或擲骰模擬器。
        * 註冊群組：[根憑證](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate)或[中繼憑證](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate)，用來產生實體裝置上的裝置憑證。  它也可能產生從擲骰模擬器。

## <a name="create-a-device-enrollment"></a>建立裝置註冊

有兩種方式可供您向佈建服務註冊裝置：

* **註冊群組**共用通用的證明機制所簽署的 X.509 憑證的裝置群組的項目[根憑證](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate)或[中繼憑證](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate). 對於共用所需初始設定的大量裝置，或是全都要進入同一個租用戶的裝置，建議您使用註冊群組。 請注意，您只能將使用 X.509 證明機制的裝置註冊為「註冊群組」。 

    您可以建立註冊群組與遵循這個工作流程 Sdk:

    1. 註冊群組的證明機制會使用 X.509 根憑證。  呼叫服務 SDK API```X509Attestation.createFromRootCertificate```根憑證建立註冊的證明。  PEM 檔案中，或以字串提供 X.509 根憑證。
    1. 建立新```EnrollmentGroup```變數使用```attestation```建立且唯一```enrollmentGroupId```。  （選擇性） 您可以設定的參數如```Device ID```， ```IoTHubHostName```， ```ProvisioningStatus```。
    2. 呼叫服務 SDK API```createOrUpdateEnrollmentGroup```應用程式後端```EnrollmentGroup```建立註冊群組。

* **個別註冊**是可能會註冊之單一裝置的項目。 個別申請可使用 X.509 憑證或 SAS 權杖 (適用於實際或虛擬 TPM) 作為證明機制。 對於需要唯一初始設定的裝置，或是只能透過 TPM 或虛擬 TPM 將 SAS 權杖作為證明機制的裝置，建議您使用個別註冊。 個別申請可能會指定所需的 IoT 中樞裝置識別碼。

    您可以遵循此工作流程 Sdk 與建立個別的註冊：
    
    1. 選擇您```attestation```機制，這可以是 TPM 或 X.509。
        1. **TPM**： 使用簽署金鑰從實體裝置或 TPM 模擬器做為輸入，您可以呼叫服務 SDK API```TpmAttestation```建立註冊的證明。 
        2. **X.509**： 使用用戶端憑證做為輸入，您可以呼叫服務 SDK API```X509Attestation.createFromClientCertificate```建立註冊的證明。
    2. 建立新```IndividualEnrollment```變數使用```attestation```建立且唯一```registrationId```做為輸入，在您的裝置上，或從 TPM 模擬器產生。  （選擇性） 您可以設定的參數如```Device ID```， ```IoTHubHostName```， ```ProvisioningStatus```。
    3. 呼叫服務 SDK API```createOrUpdateIndividualEnrollment```應用程式後端```IndividualEnrollment```建立個別的註冊。

您已成功建立註冊之後，裝置佈建服務會傳回註冊結果。

此工作流程中示範[範例](#samples)。

## <a name="update-an-enrollment-entry"></a>更新註冊項目

您已建立的註冊項目之後，您可能想要更新的註冊。  可能的案例包括更新所需的屬性、 更新的證明方法，或撤銷裝置存取權。  有不同的 Api，來個別註冊及群組註冊，但沒有差別，證明機制。

您可以更新此工作流程註冊項目：
* **個別註冊**:
    1. 取得從佈建服務的最新註冊前服務 SDK 的 api ```getIndividualEnrollment```。
    2. 修改視最新的註冊參數。 
    3. 使用最新的註冊，請呼叫服務 SDK API```createOrUpdateIndividualEnrollment```更新您的註冊項目。
* **群組註冊**:
    1. 取得從佈建服務的最新註冊前服務 SDK 的 api ```getEnrollmentGroup```。
    2. 修改視最新的註冊參數。
    3. 使用最新的註冊，請呼叫服務 SDK API```createOrUpdateEnrollmentGroup```更新您的註冊項目。

此工作流程中示範[範例](#samples)。

## <a name="remove-an-enrollment-entry"></a>移除註冊項目

* **個別註冊**可藉由呼叫服務 SDK API 刪除```deleteIndividualEnrollment```使用```registrationId```。
* **群組註冊**可藉由呼叫服務 SDK API 刪除```deleteEnrollmentGroup```使用```enrollmentGroupId```。

此工作流程中示範[範例](#samples)。

## <a name="bulk-operation-on-individual-enrollments"></a>在個別的註冊項目上的大量作業

您可以執行大量作業，以建立、 更新或移除多個個別的註冊，遵循這個工作流程：

1. 建立包含多個變數```IndividualEnrollment```。  這個變數的實作是不同的每種語言。  檢閱 GitHub 上的大量作業範例，如需詳細資訊。
2. 呼叫服務 SDK API```runBulkOperation```與```BulkOperationMode```所需的操作和您的變數，針對個別的註冊項目。 支援四種模式： 建立、 更新 updateIfMatchEtag，和刪除。

已成功地執行作業之後，裝置佈建服務會傳回大量的作業結果。

此工作流程中示範[範例](#samples)。
