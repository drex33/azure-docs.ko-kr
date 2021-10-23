---
title: IoT 커넥터에 대한 관련 GitHub 프로젝트 - Azure Healthcare API
description: IoT 커넥터에 대한 모든 GitHub(오픈 소스) 리포지토리 나열
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/19/2021
ms.author: jasteppe
ms.openlocfilehash: e66a6f3e2603645be4efeb68211ffd4d785067db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259414"
---
# <a name="open-source-projects"></a>오픈 소스 프로젝트

IoT 커넥터를 사용하여 다양한 용도로 서비스를 배포하기 위한 소스 코드와 지침을 제공하는 GitHub 오픈 소스 프로젝트를 확인하세요. 

## <a name="iot-connector-github-projects"></a>IoT 커넥터 GitHub 프로젝트

#### <a name="fhir-integration"></a>FHIR 통합

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)Azure Healthcare API IoT 커넥터 관리형 서비스의 오픈 소스 버전입니다. FHIR R7&#174;지원하는 모든 [전자 의료 기록 교환(FHIR&#174;)](https://https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491) 서비스와 함께 사용할 수 있습니다.

#### <a name="device-and-fhir-destination-mappings"></a>디바이스 및 FHIR 대상 매핑

* [IoMT 커넥터 데이터 매퍼:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)IoT 커넥터 디바이스 및 FHIR 대상 매핑을 편집, 테스트 및 문제 해결을 위한 도구입니다. Azure Portal IoT 커넥터에 업로드하기 위한 매핑을 내보내거나 오픈 소스 버전과 함께 사용합니다.

#### <a name="wearables-integration"></a>웨어러블 통합

Fitbit

* [microsoft/fitbit-on-fhir:](https://github.com/microsoft/FitbitOnFHIR)Fitbit&#174; 데이터를 FHIR 서비스로 가져옵니다.

HealthKit

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Apple&#174; HealthKit&#174; 데이터를 FHIR 서비스로 가져옵니다.

* [microsoft/healthkit-to-fhir:](https://github.com/microsoft/healthkit-to-fhir)HKObjects에서 FHIR 리소스를 만드는 간단한 방법을 제공합니다.

상태 데이터 동기화

* [microsoft/health-data-sync:](https://github.com/microsoft/health-data-sync)HealthKit 데이터를 외부 저장소로 내보내기를 간소화하고 자동화하는 Swift 라이브러리입니다.

## <a name="next-steps"></a>다음 단계
Azure Portal IoT 커넥터를 배포하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[IoT 커넥터 관리 서비스 배포](deploy-iot-connector-in-azure.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.