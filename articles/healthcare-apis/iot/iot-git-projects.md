---
title: IoT 커넥터용 관련 GitHub 프로젝트-Azure 의료 api
description: IoT 커넥터용 모든 오픈 소스 (GitHub) 리포지토리 나열
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/28/2021
ms.author: jasteppe
ms.openlocfilehash: 76bf145ea76ea44d65e2c5dc3873cb51707840a5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309850"
---
# <a name="open-source-projects"></a>오픈 소스 프로젝트

원본 코드와 IoT 커넥터를 사용 하 여 다양 한 용도로 서비스를 배포 하는 지침을 제공 하는 GitHub에서 오픈 소스 프로젝트를 확인 하세요. 

## <a name="iot-connector-github-projects"></a>IoT 커넥터 GitHub 프로젝트

#### <a name="fhir-integration"></a>FHIR 통합

* [microsoft/iomt r](https://github.com/microsoft/iomt-fhir): 오픈 소스 버전의 Azure 의료 api IoT 커넥터 관리 서비스입니다. [Fhir R7](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491) 를 지 원하는 모든 신속한 의료 상호 운용성 리소스 (fhir&#174;) 서비스와 함께 사용할 수 있습니다&#174;

#### <a name="device-and-fhir-destination-mappings"></a>장치 및 FHIR 대상 매핑

* [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): IoT 커넥터 장치 및 FHIR 대상 매핑의 편집, 테스트 및 문제 해결을 위한 도구입니다. Azure Portal에서 IoT connector로 업로드 하거나 오픈 소스 버전과 함께 사용 하기 위한 매핑을 내보냅니다.

#### <a name="wearables-integration"></a>착용 식 장치용 통합

Fitbit

* [microsoft/fitbit](https://github.com/microsoft/FitbitOnFHIR)&#174; 데이터를 fhir 서비스로 가져옵니다.

HealthKit

* [microsoft/healthkit r](https://github.com/microsoft/healthkit-on-fhir): Apple&#174; healthkit&#174; 데이터를 fhir 서비스로 가져옵니다.

* [microsoft/healthkit-](https://github.com/microsoft/healthkit-to-fhir)HKOBJECTS에서 Fhir 리소스를 만드는 간단한 방법을 제공 합니다.

FHIR에서 Google 피트니스

* [microsoft/googlefit r](https://github.com/microsoft/googlefit-on-fhir): 데이터를 Google 피트니스&#174; 데이터를 fhir 서비스로 가져옵니다.

상태 데이터 동기화

* [microsoft/health-데이터 동기화](https://github.com/microsoft/health-data-sync): 외부 저장소로 HealthKit 데이터 내보내기를 간소화 하 고 자동화 하는 Swift&#174; 라이브러리입니다.

## <a name="next-steps"></a>다음 단계
Azure Portal에서 IoT connector를 배포 하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[IoT connector 관리 서비스 배포](deploy-iot-connector-in-azure.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
