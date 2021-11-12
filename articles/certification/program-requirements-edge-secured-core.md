---
title: Edge Secured-Core 인증 요구 사항
description: Edge Secured-Core 인증 요구 사항
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 35091e1ccde554ce897dc629c3c372af05650458
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293861"
---
# <a name="edge-secured-core-certification-requirements-preview"></a>Edge Secured-Core 인증 요구 사항(미리 보기) #

이 문서에서는 인증을 완료하고 Edge Secured-Core 레이블이 있는 Azure IoT 디바이스 카탈로그에 디바이스를 나열하기 위해 충족될 디바이스별 기능 및 요구 사항을 설명합니다.

## <a name="program-purpose"></a>프로그램 용도 ##
Edge Secured-Core는 Linux 또는 Windows 10 IoT와 같은 전체 운영 체제를 실행하는 IoT 디바이스에 대한 Azure Certified Device 프로그램의 증분 인증입니다. 이 프로그램을 통해 디바이스 파트너는 추가 보안 조건 집합을 충족하여 디바이스를 구분할 수 있습니다. 이 기준을 충족하는 디바이스는 다음을 보장합니다.
1. 하드웨어 기반 디바이스 ID 
2. 시스템 무결성 적용 가능 
3. 최신 상태 유지 및 원격 관리
4. 저장 데이터 보호 기능 제공
5. 전송 중 데이터 보호 기능 제공
6. 기본 제공 보안 에이전트 및 강화
## <a name="requirements"></a>요구 사항 ##

---
|이름|SecuredCore.Built-in.Security|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 Microsoft Defender for IoT로 데이터를 전송하여 디바이스가 보안 정보 및 이벤트를 보고할 수 있도록 하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사 |디바이스에서 보안 로그와 경고를 생성해야 합니다. 디바이스 로그 및 경고 메시지가 Microsoft Defender for Cloud에 전송됩니다.<ol><li>GitHub에서 보안 에이전트 다운로드 및 배포</li><li>Microsoft Defender for IoT에서 경고 메시지의 유효성을 검사합니다.</li></ol>|
|리소스|[IoT용 Azure Docs IoT Defender](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|이름|SecuredCore.Encryption.Storage|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 중요한 데이터를 비휘발성 스토리지에서 암호화할 수 있는지 유효성을 검사하기 위한 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|스토리지 암호화가 사용하도록 설정되어 있고 기본 알고리즘은 XTS-AES로, 키 길이가 128비트 이상인지 디바이스의 유효성을 검사합니다. </br></br>참고: 미리 보기 릴리스 2021년 6월은 디바이스에 DM-Crypt가 설치되고 암호화된 파티션이 있는지만 확인합니다.|
|리소스||

---
|이름|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|상태|옵션|
|Description|테스트의 목적은 보안 인클레이브가 있는지 확인하고 인클레이브가 보안 에이전트에서 액세스할 수 있는지 확인하기 위한 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|Azure Security 에이전트가 보안 인클레이브와 통신할 수 있도록 도구 집합을 통해 디바이스의 유효성을 검사합니다.|
|리소스|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|이름|SecuredCore.Hardware.Identity|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스 식별이 하드웨어에 루팅되었는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|디바이스에 TPM이 있고 TPM 인증 키를 사용하여 IoT Hub를 통해 프로비전할 수 있는지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다.|
|리소스|[DPS를 사용하여 자동 프로비전 설정](../iot-dps/quick-setup-auto-provision.md)|

---
|이름|SecuredCore.Update|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스에서 펌웨어 및 소프트웨어를 받고 업데이트할 수 있는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|파트너는 Microsoft 업데이트, [Device Update for IoT Hub(ADU)](../iot-hub-device-update/understand-device-update.md)를 통해 디바이스에 업데이트를 보낼 수 있었는지 확인합니다. Device Update for IoT Hub를 사용하는 Linux 디바이스의 경우 인증을 위해 Secured Core 테스트 프로세스 중에 .swu 업데이트 파일을 제공하고, 인증 서비스에서 [업데이트 매니페스트](../iot-hub-device-update/update-manifest.md) 파일을 생성하기 위한 디바이스 관련 정보를 제공해야 합니다.|
|리소스|[IoT Hub용 디바이스 업데이트 문서](../iot-hub-device-update/index.yml)|

---
|이름|SecuredCore.Manageability.Configuration|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스가 원격 보안 관리를 지원하는지 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|디바이스가 원격으로 관리할 수 있는 기능 및 특히 보안 구성을 지원하는지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다. 그리고 상태는 IoT Hub/Microsoft Defender for IoT에 다시 보고됩니다.|
|리소스||

---
|이름|SecuredCore.Manageability.Reset|
|:---|:---|
|상태|필수|
|설명|이 테스트의 목적은 다음의 두 가지 사용 사례에 대해 디바이스의 유효성을 검사하는 것입니다. a) 재설정(사용자 데이터 제거, 사용자 구성 제거)을 수행하는 기능, b) 문제를 초래하는 업데이트의 경우 마지막으로 알려진 양호한 상태로 디바이스 복원|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|이 기능을 지원하는 도구 집합과 제출된 설명서의 조합을 통해 디바이스의 유효성을 검사합니다. 디바이스 제조업체는 이러한 기능을 구현하여 원격 재설정을 지원할지 아니면 로컬 재설정만 지원할지 결정할 수 있습니다.|
|리소스||

---
|이름|SecuredCore.Updates.Duration|
|:---|:---|
|상태|필수|
|설명|이 정책의 목적은 디바이스를 안전한 상태로 유지하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동|
|유효성 검사|인증된 디바이스는 제출일로부터 60개월 동안 디바이스를 최신 상태로 유지하기 위해 제출 시 동의해야 합니다. 구매자와 디바이스 자체가 어떤 방식으로든 사용할 수 있는 사양은 소프트웨어 업데이트 기간을 나타내야 합니다.|
|리소스||

---
|이름|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|상태|필수|
|설명|이 정책의 목적은 제품의 취약점에 대한 보고서를 수집하고 배포하기 위한 메커니즘이 있는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동|
|유효성 검사|인증된 디바이스에 대한 취약성 보고서를 제출하고 수신하는 프로세스에 대한 문서를 검토합니다.|
|리소스||

---
|이름|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|상태|필수|
|설명|이 정책의 목적은 높은/중요한(CVSS 3.0 사용) 취약점이 수정된 후 180 일 이내에 해결되도록 하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동|
|유효성 검사|인증된 디바이스에 대한 취약성 보고서를 제출하고 수신하는 프로세스에 대한 문서를 검토합니다.|
|리소스||


---
|이름|SecuredCore.Encryption.TLS|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 필수 TLS 버전 및 암호 그룹에 대 한 지원의 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
유효성 검사|디바이스에서 최소 TLS 버전 1.2를 지원하고 다음과 같은 필수 TLS 암호 그룹을 지원하는지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다.<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|리소스| [IoT Hub의 TLS 지원](../iot-hub/iot-hub-tls-support.md) <br /> [Windows 10의 TLS 암호 그룹](/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|이름|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 업데이트에 서명해야 하는지에 대한 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|운영 체제, 드라이버, 애플리케이션 소프트웨어, 라이브러리, 패키지 및 펌웨어에 대한 업데이트가 제대로 서명되지 않거나 유효성이 검사되지 않은 경우 적용되지 않도록 도구 집합을 통해 디바이스의 유효성을 검사합니다.
|리소스||

---
|이름|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스의 부팅 무결성에 대한 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|디바이스가 부팅될 때마다 펌웨어 및 커널 서명의 유효성을 검사하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다. <ul><li>UEFI: 보안 부팅이 사용하도록 설정됨</li><li>Uboot: 확인된 부팅이 사용하도록 설정됨</li></ul> </br> </br>참고: 미리 보기 릴리스 2021년 6월은 UEFI가 있는지만 확인합니다.|
|리소스||

---
|이름|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|상태|필수|
|설명|이 테스트의 목적은 이 디바이스에서 코드 무결성을 사용할 수 있는지 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|코드 무결성이 사용되었는지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다. </br> Windows: HVCI </br> Linux: dm-verity 및 IMA|
|리소스||

---
|이름|SecuredCore.Protection.NetworkServices|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 네트워크에서 입력을 받는 애플리케이션이 상승된 권한으로 실행되고 있지 않은지 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|네트워크 연결을 수락하는 서비스가 SYSTEM 또는 루트 권한으로 실행되고 있지 않은지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다.|
|리소스||

---
|이름|SecuredCore.Protection.Baselines|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 시스템이 기준 보안 구성을 준수하는지 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|Defender for Cloud IOT 시스템 구성 벤치마크가 실행되었는지 확인하기 위해 도구 모음을 통해 유효성을 검사할 디바이스입니다.|
|리소스| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|이름|SecuredCore.Firmware.Protection|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스가 펌웨어 보안 위협으로부터 적절한 완화 기능을 가지고 있는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|다음 방식 중 하나를 통해 펌웨어 보안 위협으로부터 보호되는지 확인하기 위해 도구 집합으로 디바이스의 유효성을 검사합니다. <ul><li>DRTM + UEFI 관리 모드 완화</li><li>DRTM + UEFI 관리 모드 강화</li><li>SRTM + 런타임 펌웨어 강화를 수행하는 승인된 FW</li></ul> |
|리소스| https://trustedcomputinggroup.org/ |

---
|이름|SecuredCore.Firmware.Attestation|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스가 Microsoft Azure 증명 서비스를 원격으로 증명할 수 있는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|플랫폼 부팅 로그 및 부팅 활동 측정을 수집하고 Microsoft Azure 증명 서비스에 대해 원격으로 증명할 수 있도록 도구 집합을 통해 디바이스의 유효성을 검사합니다.|
|리소스| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|이름|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 외부에서 액세스할 수 있는 포트에서 DMA가 사용하지 않도록 설정되어 있는지 유효성을 검사하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|디바이스에 DMA 가능 외부 포트가 있는 경우 IOMMU 또는 SMMU가 사용하도록 설정되어 있고 해당 포트에 대해 구성되었는지 확인하는 도구 집합입니다.|
|리소스||

---
|이름|SecuredCore.Protection.Debug|
|:---|:---|
|상태|필수|
|설명|테스트의 목적은 디바이스의 디버그 기능이 사용하지 않도록 설정되었는지 확인하는 것입니다.|
|대상 가용성|2021|
|적용 대상|모든 디바이스|
|OS|장치 및 시스템 독립성|
|유효성 검사 유형|수동/도구|
|유효성 검사|디버그 기능을 사용하도록 설정하려면 인증이 필요한지 확인하기 위해 도구 집합을 통해 디바이스의 유효성을 검사합니다.|
|리소스||
