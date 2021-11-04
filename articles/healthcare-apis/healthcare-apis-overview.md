---
title: Azure Healthcare API란?
description: 이 문서는 Azure Healthcare API의 개요입니다.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 8078dcb2cc0db6786881a834540922e66f47eac8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468878"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Azure Healthcare API(미리 보기)란?

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

작업하는 상태 데이터는 여러 시스템 및 형식에 걸쳐 조각화됩니다. 이 데이터를 관리하는 것은 충분히 어렵기 때문에 인사이트를 얻는 것은 불가능해 보입니다. 이러한 서로 다른 모든 시스템과 데이터를 결합하는 방법을 찾아야 합니다. 의료 데이터에 대한 통합된 접근 방식을 사용하면 운영 및 임상 인사이트를 검색하거나, 새로운 최종 사용자 애플리케이션을 연결하거나, 새 연구 프로젝트를 사용하도록 설정할 수 있습니다. Azure Healthcare API는 서로 다른 PHI 집합을 통합하고 기계 학습, 분석 및 AI용 도구와 엔드투엔드 연결하여 검색된 인사이트를 통해 의료 분야를 개선할 수 있는 도구 및 커넥터 집합입니다.

Azure Healthcare API는 다음과 같은 이점을 제공합니다.
* 데이터를 일관된 방식으로 한 곳에서 수집하고 액세스할 수 있도록 하여 새 워크로드가 PHI를 활용할 수 있도록 합니다.
* 서로 다른 PHI를 결합하고 기계 학습, 분석 및 AI용 도구와 엔드투엔드 연결하여 새로운 인사이트를 검색합니다.
* 보호된 상태 정보가 관리, 저장 및 제공되는 방식에 대한 확신을 가지고 신뢰할 수 있는 클라우드에서 빌드합니다.
새로운 Microsoft Azure Healthcare API는 FHIR 외에도 DICOM과 같은 다른 의료 산업 데이터 표준을 지원하여 의료 데이터 상호 운용성을 확장합니다. 다양한 향후 의료 데이터 표준의 확장 및 도입을 수용하기 위해 비즈니스 모델 및 인프라 플랫폼이 다시 디자인되었습니다. 고객은 동일한 규정 준수를 통해 의료 표준에서 다양한 유형의 의료 데이터를 사용할 수 있습니다. 고객이 레거시 또는 디바이스 전용 형식에서 FHIR로 데이터를 변환할 수 있는 관리형 서비스에 도구가 기본 제공되었습니다. 이러한 도구 중 일부는 이전에 개발되어 오픈 소스로 사용되었습니다. 다른 내용은 net new가 됩니다.

Azure Healthcare API를 사용하면 다음을 수행할 수 있습니다. 
* 구조화, 이미징 및 디바이스 데이터와 같은 서로 다른 상태 데이터 원본 및 형식을 신속하게 연결하고 클라우드에서 유지하도록 정규화합니다.
* 데이터를 FHIR로 변환하고 수집합니다. 예를 들어 HL7v2 또는 CDA와 같은 레거시 형식에서 또는 디바이스 전용 형식의 빈도가 높은 IoT 데이터에서 FHIR로 상태 데이터를 변환할 수 있습니다.
* Synapse와 같은 Azure 에코시스템 및 Microsoft의 제품(예: Teams)을 통해 의료 API에 저장된 데이터를 커넥트 분석 및 기계 학습을 통해 새로운 인사이트를 도출하고 새 워크플로를 활성화하고 SMART on FHIR 애플리케이션에 연결할 수 있습니다.
* 안정성, 확장성 및 보안을 제공하는 엔터프라이즈 기능으로 고급 워크로드를 관리하여 데이터가 보호되고 의료 업계에 필요한 개인 정보 보호 및 규정 준수 인증을 충족하는지 확인합니다.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Azure Healthcare API와 Azure API for FHIR 간의 주요 차이점은 무엇인가요?

**연결된 서비스**

이제 Azure Healthcare API는 구조적 데이터 교환을 위해 여러 상태 데이터 표준을 지원합니다. Azure Healthcare API의 단일 컬렉션을 사용하면 서로 원활하게 작동하는 다양한 서비스 유형(FHIR 서비스, DICOM 서비스 및 IoT Connector)의 여러 인스턴스를 배포할 수 있습니다.

**DICOM 서비스 소개**

Azure Healthcare API에는 이제 DICOM 서비스에 대한 지원이 포함됩니다. DICOM을 사용하면 이미지 데이터와 관련 메타데이터를 안전하게 교환할 수 있습니다. DICOM은 의료 이미징 정보를 전송, 저장, 검색, 인쇄, 처리 및 표시하는 국제 표준이며 의료 전반에 허용되는 기본 의료 이미징 표준입니다. DICOM 서비스에 대한 자세한 내용은 [DICOM 개요를 참조하세요.](./dicom/dicom-services-overview.md)

**FHIR 서비스에 대한 증분 변경**

FHIR 데이터의 안전한 교환을 위해 의료 API는 Azure API for FHIR 사용할 수 없는 몇 가지 증분 기능을 제공합니다. 
* 트랜잭션 지원: 의료 API에서 FHIR 서비스는 트랜잭션 번들을 지원합니다. 트랜잭션 번들에 대한 자세한 내용은 HL7.org 방문하여 일괄 처리/트랜잭션 상호 작용을 참조하세요.
* 연결된 검색 개선 사항: 연결된 검색 & 예약 체인 검색은 더 이상 하위 쿼리당 100개 항목으로 제한되지 않습니다.


## <a name="next-steps"></a>다음 단계

Azure Healthcare API 작업을 시작하려면 5분 빠른 시작에 따라 작업 영역 배포를 수행합니다.

> [!div class="nextstepaction"]
> [Azure Portal 작업 영역 배포](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [작업 영역 개요](workspace-overview.md)
