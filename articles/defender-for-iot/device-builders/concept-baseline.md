---
title: 기준 및 사용자 지정 검사
description: IoT 기준에 대 한 Microsoft Defender의 개념에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 80cb204008c60964ed90c63885ce8b99d23ca30b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284135"
---
# <a name="microsoft-defender-for-iot-baseline-and-custom-checks"></a>IoT 기준 및 사용자 지정 확인을 위한 Microsoft Defender

이 문서에서는 Defender for IoT 기준을 설명하고 기준 사용자 지정 검사의 모든 관련 속성을 요약합니다.

## <a name="baseline"></a>기초

기준은 각 디바이스에 대한 표준 동작을 설정하고 예상되는 표준의 비정상적인 동작 또는 편차를 보다 쉽게 설정할 수 있습니다.

## <a name="baseline-custom-checks"></a>기준 사용자 지정 검사

기준 사용자 지정 검사는 디바이스의 **모듈 ID 쌍** 을 사용하여 각 디바이스 기준에 대한 사용자 지정 검사 목록을 설정합니다.

## <a name="setting-baseline-properties"></a>기준 속성 설정

1. IoT Hub에서 변경하려는 디바이스를 찾아서 선택합니다.

1. 디바이스를 클릭한 다음 **azureiotsecurity** 모듈을 클릭합니다.

1. **모듈 ID 쌍** 을 클릭합니다.

1. **기준 사용자 지정 검사** 파일을 디바이스에 업로드합니다.

1. 기준 속성을 Defender-IoT-micro-agent에 추가하고 **저장** 을 클릭합니다.

### <a name="baseline-custom-check-file-example"></a>기준 사용자 지정 검사 파일 예제

기준 사용자 지정 검사 구성 방법:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>기준 사용자 지정 검사 속성

| 이름| 상태 | 유효한 값| 기본값| Description |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|필수: true |유효한 값: **부울** |기본값: **false** |우선 순위가 높은 메시지가 전송되기 전의 최대 시간 간격입니다.|
|baselineCustomChecksFilePath |필수: true|유효한 값: **문자열**, **Null** |기본값: **Null** |기준 xml 구성의 전체 경로|
|baselineCustomChecksFileHash |필수: true|유효한 값: **문자열**, **Null** |기본값: **Null** |XML 구성 파일의 `sha256sum`. 추가 정보는 [sha256sum 참조](https://linux.die.net/man/1/sha256sum)를 사용합니다. |

추가 기준 예제를 검토하려면 [사용자 지정 기준 예제-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) 및 [사용자 지정 기준 예제-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [원시 보안 데이터](how-to-security-data-access.md)에 액세스
- [디바이스 조사](how-to-investigate-device.md)
- [보안 권장 사항](concept-recommendations.md) 이해 및 탐색
- [보안 경고](concept-security-alerts.md) 이해 및 탐색
