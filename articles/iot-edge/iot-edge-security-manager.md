---
title: 了解安全性管理員如何保護裝置 (軟體) - Azure IoT Edge | Microsoft Docs
description: 管理 IoT Edge 裝置安全性立場和安全性服務的完整性。
services: iot-edge
keywords: 安全性、安全性元素、enclave、TEE、IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457525"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全性管理員

Azure IoT Edge 安全性管理員是有完整界限的安全性核心，它透過將安全晶片硬體抽象化，來保護 IoT Edge 裝置與其所有元件。 It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Azure IoT Edge 安全性管理員](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 安全性管理員的目標是保護 IoT Edge 裝置與所有固有軟體作業的完整性。 The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  IoT Edge 安全性管理員是搭配可用安全晶片硬體運作的軟體，有助於提供最高安全性保證。  

IoT Edge 安全性管理員的責任包括但不限於：

* Azure IoT Edge 裝置的啟動受保護且受測量。
* 佈建裝置身分識別，以及在適用的情況下轉換信任。
* 裝載及保護雲端服務 (如裝置佈建服務) 的裝置元件。
* 使用唯一身分識別安全地佈建 IoT Edge 模組。
* 透過公證服務作為硬體根信任的閘道管理員。
* 在執行階段監視 IoT Edge 作業的完整性。

IoT Edge 安全性管理員包含三項元件：

* IoT Edge 安全性精靈。
* 硬體安全性模組平台抽象層 (HSM PAL)。
* 硬體晶片根信任或 HSM，雖然是選擇性，但強烈建議包含它。

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 安全性精靈

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>設計原則

IoT Edge 安全性精靈會遵循兩個核心原則：達到最大的作業完整性，並且盡可能降低膨脹和變換。

#### <a name="maximize-operational-integrity"></a>將運作完整性最大化

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. 透過適當的整合，根信任硬體會靜態地以及在執行階段測量及監視安全性精靈，以防止竄改。

實體存取一向對於 IoT 裝置構成威脅。 硬體根信任在防禦 IoT Edge 安全性精靈的完整性中扮演重要角色。  Hardware root of trust come in two varieties:

* 適用於敏感性資訊 (如祕密和密碼編譯金鑰) 保護的安全元素。
* 適用於祕密保護 (如金鑰) 與敏感性工作負載 (如計量和計費) 的安全性保護區。

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>將膨脹和變動最小化

IoT Edge 安全性精靈的另一個核心原則是將變換最小化。  針對最高層級的信任，IoT Edge 安全性精靈可與裝置硬體根信任緊密耦合，並以原生程式碼的方式運作。  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  範例包括跳過更新以將作業可用性最大化，或根信任硬體太受限而無法處理大型更新承載。  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 安全性精靈的架構

![Azure IoT Edge 安全性精靈](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>雲端介面

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>管理 API

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. 這些註冊將模組的身分識別對應到模組的某些屬性。 這些屬性的一些範例為在容器中執行之處理序的處理序識別碼 (pid)，或是 Docker 容器內容的雜湊。

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  由於 IoT Edge 安全性精靈會啟動 (Bootstrap) 及啟動 (Start) IoT Edge 代理程式，所以它能在證明 IoT Edge 代理程式沒有被竄改之後，為 IoT Edge 代理程式建立隱含的註冊。 The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>容器 API

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>工作負載 API

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. 信任組合包含模組應該信任之所有其他伺服器的 CA 憑證。

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. 如果成功，它會使用註冊的屬性來測量模組。 If the result of the measurement process matches the registration, a new proof of identity is generated. 對應的 CA 憑證 (信任組合) 會傳回到模組。  模組使用此憑證來連線到 IoT 中樞、其他模組或用來啟動伺服器。 When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>整合和維護

Microsoft [在 GitHub 上維護 IoT Edge 安全性精靈](https://github.com/Azure/iotedge/tree/master/edgelet) \(英文\) 的主要程式碼基底。

#### <a name="installation-and-updates"></a>安裝和更新

IoT Edge 安全性精靈的安裝和更新是透過作業系統的套件管理系統來管理。 IoT Edge 裝置應該有硬體根信任，並且通常會透過安全性開機和更新管理系統來為精靈的完整性提供額外的強化。 Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>版本控制

IoT Edge 執行階段會追蹤並回報 IoT Edge 安全性精靈的版本。 版本是回報為 IoT Edge 代理程式模組報告屬性 (property) 的 *runtime.platform.version* 屬性 (attribute)。

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>硬體安全性模組平台抽象層 (HSM PAL)

HSM PAL 將所有根信任硬體抽象化，以將 IoT Edge 開發人員或使用者從其複雜性中分離。  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  HSM PAL 的實際實作取決於使用中的特定安全硬體而定。 它的存在使幾乎任何安全晶片硬體都能使用。

## <a name="secure-silicon-root-of-trust-hardware"></a>安全晶片根信任硬體

必須有安全晶片才能在 IoT Edge 裝置硬體內固定信任。  安全晶片有各種類型，包括信賴平台模組 (TPM)、內嵌安全性元素 (eSE)、ARM 信任區、Intel SGX 及自訂安全晶片技術。  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 安全性管理員整合和維護

對於自訂強化功能，IoT Edge 安全性管理員能夠識別並隔離負責維護 Azure IoT Edge 平台安全性和完整性的元件。 裝置製造商等協力廠商應該利用可和其裝置硬體搭配使用的自訂安全性功能。  請參閱＜後續步驟＞一節，其中的連結顯示如何在 Linux 和 Windows 平台上使用信賴平台模組 (TPM) 來強化 Azure IoT 安全性管理員。 這些範例是使用軟體或虛擬 TPM，但也直接套用至使用離散 TPM 裝置的情形。  

## <a name="next-steps"></a>後續步驟

閱讀部落格文章[保護智慧邊緣](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) \(英文\)。

[在 Linux 虛擬機器上使用虛擬 TPM](how-to-auto-provision-simulated-device-linux.md) 建立及佈建 IoT Edge 裝置。

[在 Windows 上建立並佈建模擬 TPM 的 IoT Edge 裝置](how-to-auto-provision-simulated-device-windows.md)。
