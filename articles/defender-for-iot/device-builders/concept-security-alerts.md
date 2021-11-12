---
title: 기본 제공 & 사용자 지정 경고 목록
description: Defender for IoT Hub의 기능 및 서비스를 사용하여 보안 경고 및 권장 수정 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 89fb77bed6116fcbae847b19b79e90d4dff34a23
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293538"
---
# <a name="defender-for-iot-hub-security-alerts"></a>Defender for IoT Hub 보안 경고

Defender for IoT는 고급 분석 및 위협 인텔리전스를 사용하여 IoT 솔루션을 지속적으로 분석하여 악의적인 활동을 경고합니다.
또한 예상되는 디바이스 동작에 대한 정보를 기반으로 사용자 지정 경고를 만들 수 있습니다.
경고는 잠재적 손상을 나타내는 표시기 역할을 하며, 조사를 거쳐 수정해야 합니다.

이 문서에서는 IoT Hub에서 트리거할 수 있는 기본 제공 경고 목록을 찾을 수 있습니다.
Defender for IoT를 사용하면 기본 제공 경고 외에도 예상되는 IoT Hub 및/또는 디바이스 동작을 기반으로 사용자 지정 경고를 정의할 수 있습니다.
자세한 정보는 [사용자 지정할 수 있는 경고](concept-customizable-security-alerts.md)를 참조하세요.

## <a name="built-in-alerts-for-iot-hub"></a>IoT Hub에 대한 기본 제공 경고

### <a name="medium-severity"></a>중간 심각도

| 이름 | 심각도 | 데이터 원본 | Description | 제안된 수정 사항 | AlertType |
|--|--|--|--|--|--|
| IoT Hub에 새 인증서가 추가됨 | 중간 | IoT Hub | 인증서가 IoT Hub에 추가 되었습니다. 권한 없는 주체가 해당 작업을 수행한 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 1. 권한 있는 주체가 인증서를 추가했는지 확인합니다. <br> 2. 권한 있는 주체가 인증서를 추가하지 않은 경우 해당 인증서를 제거하고 조직 보안 팀에 경고를 에스컬레이션합니다. | IoT_CertificateSuccessfullyAddedToHub |
| IoT Hub에서 인증서가 삭제됨 | 중간 | IoT Hub | IoT Hub에서 인증서가 삭제 되었습니다. 권한 없는 주체가 해당 작업을 수행한 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 1. 권한 있는 주체가 인증서를 제거했는지 확인합니다. <br> 2. 권한 있는 주체가 인증서를 제거하지 않은 경우 해당 인증서를 다시 추가하고 조직 보안 팀에 경고를 에스컬레이션합니다. | IoT_CertificateSuccessfullyDeletedFromHub |
| IoT Hub에 인증서를 추가하려다 실패한 시도가 탐지됨 | 중간 | IoT Hub | IoT Hub에 인증서를 추가 하지 못했습니다. 권한 없는 주체가 해당 작업을 수행한 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 권한 있는 주체에게만 인증서를 변경할 권한이 부여되었는지 확인합니다. | Hub_CertificateFailedToBeAddedToHub |
| IoT Hub에서 인증서를 삭제하려다 실패한 시도가 탐지됨 | 중간 | IoT Hub | IoT Hub에서 인증서를 삭제 하지 못했습니다. 권한 없는 주체가 해당 작업을 수행한 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 권한 있는 주체에게만 인증서를 변경할 권한이 부여되었는지 확인합니다. | IoT.Hub_CertificateFailedToBeDeletedFromHub |
| x.509 디바이스 인증서 지문 불일치 | 중간 | IoT Hub | x.509 디바이스 인증서 지문이 구성과 일치하지 않습니다. | 디바이스에서 경고를 검토하세요. 추가 작업이 필요하지 않습니다. | IoT_Cert_Print_Mismatch |
| x.509 인증서가 만료됨 | 중간 | IoT Hub | X.509 디바이스 인증서가 만료되었습니다. | 만료 된 인증서가 있는 합법적인 장치 이거나 합법적인 장치를 가장할 수 있습니다. 합법적인 디바이스가 현재 올바르게 통신하고 있는 경우 이는 가장 시도일 가능성이 높습니다. | IoT_Cert_Expired |

### <a name="low-severity"></a>낮은 심각도

| 이름 | 심각도 | 데이터 원본 | Description | 제안된 수정 사항 | AlertType |
|--|--|--|--|--|--|
| IoT Hub의 진단 설정을 추가하거나 편집하려는 시도가 탐지됨 | 낮음 | IoT Hub | IoT Hub의 진단 설정을 추가하거나 편집하려는 시도가 탐지되었습니다. 진단 설정을 사용하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. 권한 있는 주체가 해당 작업을 수행하지 않은 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 1. 권한 있는 주체가 인증서를 제거했는지 확인합니다. <br> 2. 권한 있는 주체가 인증서를 제거하지 않은 경우 해당 인증서를 다시 추가하고 정보 보안 팀에 경고를 에스컬레이션합니다. | IoT_DiagnosticSettingAddedOrEditedOnHub |
| IoT Hub의 진단 설정을 삭제하려는 시도가 탐지됨 | 낮음 | IoT Hub | IoT Hub의 진단 설정을 추가하거나 편집하려는 시도가 탐지되었습니다. 진단 설정을 사용하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. 권한 있는 주체가 해당 작업을 수행하지 않은 경우 해당 작업은 악의적인 활동을 나타낼 수 있습니다. | 권한 있는 주체에게만 진단 설정을 변경할 권한이 부여되었는지 확인합니다. | IoT_DiagnosticSettingDeletedFromHub |
| SAS 토큰 만료됨 | 낮음 | IoT Hub | 디바이스에서 만료된 SAS 토큰을 사용함 | 만료 된 토큰이 있는 합법적인 장치 이거나 합법적인 장치를 가장할 수 있습니다. 합법적인 디바이스가 현재 올바르게 통신하고 있는 경우 이는 가장 시도일 가능성이 높습니다. | IoT_Expired_SAS_Token |
| 잘못된 SAS 토큰 서명 | 낮음 | IoT Hub | 디바이스에서 사용하는 SAS 토큰에 잘못된 서명이 있습니다. 서명이 기본 또는 보조 키와 일치하지 않습니다. | 디바이스에서 경고를 검토하세요. 추가 작업이 필요하지 않습니다. | IoT_Invalid_SAS_Token |

## <a name="next-steps"></a>다음 단계

- Defender for IoT 서비스 [개요](overview.md)
