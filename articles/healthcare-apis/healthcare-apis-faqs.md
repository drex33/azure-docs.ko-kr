---
title: Azure 의료 Api에 대 한 Faq
description: 이 문서에서는 Azure 의료 Api에 대 한 질문과 대답을 제공 합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: ginle
ms.openlocfilehash: 6ce51e4cf916baaa22d14e902872185901b4d5da
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988618"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Azure 의료 Api (미리 보기)에 대 한 질문과 대답

Azure 의료 Api에 대 한 몇 가지 질문과 대답은 다음과 같습니다.

## <a name="azure-healthcare-apis-the-basics"></a>Azure 의료 Api: 기본 사항

### <a name="what-is-the-azure-healthcare-apis"></a>Azure 의료 Api 란?
Azure 의료 Api는 완벽 하 게 관리 되는 상태 데이터 플랫폼으로, 빠른 의료 (정보 상호 운용성® 리소스) 및 의약품 (DICOM®)의 디지털 이미징 및 통신과 같은 상호 운용 가능한 개방형 업계 표준을 통해 보호 된 상태 정보 (예: 보호 된 상태 정보) 및 상태 데이터의 지 속성을 빠르게 교환할 수 있게 해 줍니다.

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>Azure 의료 Api를 통해 수행할 수 있는 작업은 무엇 인가요?
Azure 의료 Api를 사용 하면 다음을 수행할 수 있습니다. 

* 구조화 된, 이미징 및 장치 데이터와 같은 서로 다른 상태 데이터 원본 및 형식을 빠르게 연결 하 고 클라우드에서 유지 되도록 정규화 합니다.

* 데이터를 변환 하 고 데이터를 FHIR로 수집 합니다. 예를 들어 HL7v2 또는 CDA와 같은 레거시 형식 또는 장치 소유 형식의 높은 빈도 IoT 데이터에서 FHIR로 상태 데이터를 변환할 수 있습니다.

* 분석 및 기계 학습을 통해 새로운 통찰력을 얻고 새 워크플로를 사용 하도록 설정 하 고 fhir 응용 프로그램에 대 한 연결을 설정 하는 등, Synapse와 같은 Teams Microsoft의 제품과 같은 Azure 에코 시스템의 서비스를 통해 의료 api에 저장 된 데이터를 커넥트 합니다.

* 안정성, 확장성 및 보안을 제공 하는 엔터프라이즈 기능을 통해 고급 작업을 관리 하 여 데이터 보호를 보장 하 고 의료 업계에 필요한 개인 정보 및 규정 준수 인증을 충족 합니다.

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>Azure API for FHIR에서 의료 Api로 기존 프로덕션 워크 로드를 마이그레이션할 수 있나요?
아니요, 지금은 마이그레이션 기능을 제공 하지 않습니다. 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>Azure 의료 Api의 가격은 무엇 인가요?
공개 미리 보기 단계 중에는 Azure 의료 Api를 무료로 사용할 수 있습니다.

### <a name="what-regions-are-healthcare-apis-available"></a>의료 Api를 사용할 수 있는 지역은 어디 인가요?
최신 정보는 [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir) 페이지를 참조 하세요. 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>Azure 의료 Api에 대 한 구독 할당량 제한은 무엇 인가요?

#### <a name="workspace-logical-container"></a>작업 영역 (논리적 컨테이너):
* 구독 당 200 인스턴스 (조정 불가능)

#### <a name="dicom-service"></a>DICOM 서비스:
* 구독 당 800 인스턴스 (조정 불가능)
* 작업 영역 당 DICOM 인스턴스 10 개 (조정 불가능)

#### <a name="fhir-service"></a>FHIR 서비스:
* 구독 당 25 개 인스턴스 (조정 불가능)
* 작업 영역 당 10 개 인스턴스 (조정할 수 없음)

#### <a name="iot-connector"></a>IoT 커넥터:
* 구독 당 25 개 IoT 커넥터 (조정 불가능)
* 작업 영역 당 10 개의 IoT 커넥터 (조정 불가능)
* IoT 커넥터 당 단일 FHIR 대상 * (조정 불가능)

(*-FHIR 대상은 IoT 커넥터의 자식 리소스)

## <a name="more-frequently-asked-questions"></a>기타 질문과 대답
[Azure 의료 Api FHIR 서비스에 대 한 Faq](./fhir/fhir-faq.md)

[Azure 의료 Api DICOM service에 대 한 Faq](./dicom/dicom-services-faqs.yml)

[Azure 의료 Api IoT 커넥터에 대 한 Faq](./iot/iot-connector-faqs.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
