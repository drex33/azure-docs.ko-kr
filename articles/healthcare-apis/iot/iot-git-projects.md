---
title: IoT 커넥터용 관련 GitHub 프로젝트-Azure 의료 api
description: IoT 커넥터용 모든 오픈 소스 (GitHub) 리포지토리 나열
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187780"
---
# <a name="open-source-projects"></a>오픈 소스 프로젝트

원본 코드와 IoT 커넥터를 사용 하 여 다양 한 용도로 서비스를 배포 하는 지침을 제공 하는 GitHub에서 오픈 소스 프로젝트를 확인 하세요. 

## <a name="iot-connector-github-projects"></a>IoT 커넥터 GitHub 프로젝트

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub 및 IoT Central와 통합

* [microsoft/iomt r](https://github.com/microsoft/iomt-fhir): IoT Hub 또는 IoT Central와 통합 되어 정규화 된 데이터의 데이터 정규화 및 fhir 변환으로 신속한 의료 기능 상호 운용성 리소스 (fhir&#174;)를 통합 합니다.
* 정규화: 추가 처리를 위해 장치 데이터 정보가 공통 형식으로 추출 됩니다.
* FHIR 변환: 정규화 된 데이터와 그룹화 된 데이터는 FHIR에 매핑됩니다. 관찰은 구성 된 템플릿에 따라 만들어지거나 업데이트 되며 장치 및 환자에 연결 됩니다.

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>장치 및 FHIR 대상 매핑 작성 및 문제 해결

* [장치 및 fhir 대상 매핑을 빌드하는 데 도움이](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)되는 도구: 장치 입력 데이터를 표준화 하 고 fhir 리소스로 변환 하기 위한 매핑 구성을 시각화 합니다. 개발자는이 도구를 사용 하 여 장치 매핑과 FHIR 대상 매핑을 편집 하 고 테스트할 수 있습니다. Azure Portal에서 IoT connector로 업로드 하기 위해 내보냅니다.

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 및 FHIR 통합

* [microsoft/healthkit r](https://github.com/microsoft/healthkit-on-fhir): fhir 서비스에 대 한 Apple healthkit 데이터 내보내기를 자동화 하는 Swift 라이브러리입니다.

## <a name="next-steps"></a>다음 단계
Azure Portal에서 IoT connector를 배포 하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[IoT connector 관리 서비스 배포](deploy-iot-connector-in-azure.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.