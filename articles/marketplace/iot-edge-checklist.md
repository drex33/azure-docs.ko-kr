---
title: Azure Marketplace의 IoT Edge 모듈 제품에 대한 사전 인증 검사 목록
description: Azure Marketplace에서 IoT Edge 모듈 제품을 게시하기 위한 특정 인증 요구 사항에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 1f9ad2fd05e11d981dd374919e2cc43d1b8d1f97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232312"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>IoT Edge 모듈의 사전 인증 검사 목록

> [!NOTE]
> 게시자는 인증을 제출하기 전에 이 검사 목록을 검토하고 모듈 기능의 유효성을 검사하기를 적극 권장합니다. 이렇게 하면 변경하고 다시 제출할 필요가 줄어 인증 프로세스가 가속화됩니다.

## <a name="validation-of-image"></a>이미지 유효성 검사

Edge 모듈 이미지를 제출할 준비가 되면 다음 단계를 수행하여 이미지가 Microsoft에서 기대한 방식으로 작동하는지 확인합니다.

### <a name="steps-to-perform-in-the-azure-portal"></a>Azure Portal에서 수행할 단계

1. [Azure Portal](https://partner.microsoft.com/)을 엽니다.
1. 리소스 그룹을 만듭니다.
1. IoT Hub를 만듭니다.
1. IoT Edge 디바이스를 만듭니다.
1. 연결 문자열을 메모장에 복사하고 저장합니다.
1. **만들어진 Edge 디바이스의 모듈** 집합을 선택합니다.
1. 최신 버전의 이미지가 있는 곳에 ACR 세부 정보를 추가합니다.
1. **IoT Edge 모듈 추가** 를 선택하고 다음을 입력합니다.
    - 모듈 설정의 이미지 URI
    - 환경 변수(파트너 센터에 추가되는 것과 같음)
    - 컨테이너 만들기 옵션(파트너 센터에 추가된 것과 같음)
    - 모듈 쌍 설정(파트너 센터에 추가된 것과 같음)
1. 경로 추가(파트너 센터에 추가된 것과 같음).
1. **검토 + 생성** 를 선택합니다.

Edge 모듈은 Azure에서 만든 Edge 디바이스에 배포됩니다.

### <a name="steps-to-perform-on-the-device"></a>디바이스에서 수행하는 단계

#### <a name="device-details"></a>디바이스 세부 정보

인증 팀은 다음 하드웨어를 사용하여 여러 아키텍처에서 이미지의 유효성을 검사합니다.

- X64 이미지의 경우 Ubuntu Server 18.04/Ubuntu Server 16.04를 실행하는 표준 D2s v3로 구성 크기가 설정된 Azure VM입니다.
- Azure Resource Manager 32 이미지의 경우 Raspberry Pi 3 모델 B입니다.
- Azure Resource Manager 64 이미지의 경우 NVIDIA Jetson Nano 4Gb입니다.

#### <a name="steps"></a>단계

1. 만들어진 디바이스/VM에 Putty를 통해 액세스할 수 있는지 확인합니다.
1. [IoT Edge 런타임](../iot-edge/how-to-provision-single-device-linux-symmetric.md)을 디바이스에 다운로드합니다.
1. 5단계에서 복사한 연결 문자열을 config.yaml 파일로 업데이트합니다.
1. Edge 모듈을 `sudo systemctl restart iotedge`을 이용해 다시 시작합니다.
1. 모듈이 디바이스에 배포되었는지 `sudo iotedge list`로 확인합니다. 실행 상태여야 합니다.
1. `sudo iotedge logs “Module Name“ -f`을 이용해 배포된 모듈의 로그에 오류가 없는지 확인합니다. 알려진 오류가 있는 경우 제품을 제출하기 전에 파트너 센터 **검토자 메모** 에 설명합니다.

## <a name="metadata-validation"></a>메타데이터 유효성 검사

다음을 확인합니다.

- 최신 태그는 파트너 센터와 Azure Container Registry 양쪽 모두에 나열됩니다.
- 최소 하드웨어 요구 사항이 제품 설명에 추가됩니다.
- 파트너 센터에서 Azure Container Registry 사용자 이름 및 암호를 업데이트하고 추가합니다.
- 원하는 **트윈 속성** 의 정확도 *(해당하는 경우)* .
- 원하는 **환경 변수** 의 정확도 *(해당하는 경우)* .
- 원하는 **만들기 옵션** 의 정확도 *(해당하는 경우)* .
- 리드 관리 연결 문자열이 있습니다.
- 개인정보처리방침 있음
- 사용 약관 있음
- 지원되는 IoT Edge 디바이스 링크를 여기서 추가하세요. [Azure IoT 디바이스 카탈로그](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 모듈 배포](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [파트너 센터에 Edge 모듈 게시](./iot-edge-offer-setup.md)
- [IoT Edge 모듈 배포](../iot-edge/quickstart-linux.md)