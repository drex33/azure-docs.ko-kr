---
title: Azure 의료 Api 란?
description: 이 문서는 Azure 의료 Api에 대 한 개요입니다.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: 01d808813d944d243582cc631f960a241061b46f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786140"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>Azure 의료 Api (미리 보기) 란?

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

작업 하는 상태 데이터는 여러 시스템 및 형식에서 조각화 됩니다. 이 데이터를 관리 하는 것 만으로는 충분 하지 않습니다. 이러한 모든 서로 다른 시스템과 데이터를 함께 가져오는 방법을 찾아야 합니다. 상태 데이터에 대 한 통합 된 접근 방식을 통해 운영 및 임상 정보를 검색 하 고, 새 최종 사용자 응용 프로그램을 연결 하거나, 새 리서치 프로젝트를 사용 하도록 설정할 수 있습니다. Azure 의료 Api는 개별 화 된 프로그램 집합을 결합 하 고 기계 학습, 분석 및 AI 용 도구를 사용 하 여 종단 간 연결을 통해 발견 된 정보를 통해 의료 분야를 개선할 수 있는 도구 및 커넥터 집합입니다.

Azure 의료 Api는 다음과 같은 이점을 제공 합니다.
* 일관 된 방식으로 데이터를 한 곳에서 수집 하 고 액세스할 수 있도록 하 여 새 워크 로드를 새 워크 로드에 강화 합니다.
* 별도의 화 된 사용을 결합 하 고 machine learning, 분석 및 AI 용 도구를 사용 하 여 종단 간 연결을 통해 새로운 통찰력을 얻을 수 있습니다.
* 보호 된 상태 정보를 관리, 저장 및 사용 가능 하 게 하는 방법에 대 한 확신을가지고 신뢰할 수 있는 클라우드에서 빌드 하세요.
새로운 Microsoft Azure 의료 api는 fhir 외에도 DICOM와 같은 다른 의료 업계 데이터 표준을 지원 하 여 의료 데이터 상호 운용성을 확장 합니다. 비즈니스 모델 및 인프라 플랫폼은 다양 한 향후 의료 데이터 표준의 확장 및 도입을 수용 하도록 다시 설계 되었습니다. 고객은 동일한 규정 준수에 따라 의료 표준에서 다양 한 유형의 상태 데이터를 사용할 수 있습니다. 고객이 레거시 또는 장치 소유 형식에서 데이터를 FHIR로 변환할 수 있는 관리 되는 서비스에 도구가 내장 되어 있습니다. 이러한 도구 중 일부는 이전에 개발 되 고 오픈 소스가 되었습니다. 다른 항목은 net new입니다.

Azure 의료 Api를 사용 하면 다음을 수행할 수 있습니다. 
* 구조화 된, 이미징 및 장치 데이터와 같은 서로 다른 상태 데이터 원본 및 형식을 빠르게 연결 하 고 클라우드에서 유지 되도록 정규화 합니다.
* 데이터를 변환 하 고 데이터를 FHIR로 수집 합니다. 예를 들어 HL7v2 또는 CDA와 같은 레거시 형식 또는 장치 소유 형식의 높은 빈도 IoT 데이터에서 FHIR로 상태 데이터를 변환할 수 있습니다.
* 분석 및 기계 학습을 통해 새로운 통찰력을 얻고 새 워크플로를 사용 하도록 설정 하 고 fhir 응용 프로그램에 대 한 연결을 설정 하는 등, Synapse와 같은 Teams Microsoft의 제품과 같은 Azure 에코 시스템의 서비스를 통해 의료 api에 저장 된 데이터를 커넥트 합니다.
* 안정성, 확장성 및 보안을 제공 하는 엔터프라이즈 기능을 통해 고급 작업을 관리 하 여 데이터 보호를 보장 하 고 의료 업계에 필요한 개인 정보 및 규정 준수 인증을 충족 합니다.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>Azure 의료 Api와 FHIR 용 Azure API의 주요 차이점은 무엇 인가요?

**연결된 서비스**

Azure 의료 Api는 이제 구조화 된 데이터의 교환에 대 한 여러 상태 데이터 표준을 지원 합니다. Azure 의료 api의 단일 컬렉션을 사용 하면 서로 원활 하 게 작동 하는 다양 한 서비스 유형 (fhir 서비스, DICOM service 및 IoT Connector)의 여러 인스턴스를 배포할 수 있습니다.

**DICOM 서비스 소개**

Azure 의료 Api는 이제 DICOM 서비스에 대 한 지원을 포함 합니다. DICOM를 사용 하면 이미지 데이터와 연결 된 메타 데이터를 안전 하 게 교환할 수 있습니다. DICOM는 의료 이미징 정보를 전송, 저장, 검색, 인쇄, 처리 및 표시 하는 국제 표준 이며 의료 분야에서 승인 된 기본 의료 이미징 표준입니다. DICOM 서비스에 대 한 자세한 내용은 [Dicom 개요](./dicom/dicom-services-overview.md)를 참조 하세요.

**FHIR 서비스에 대 한 증분 변경**

FHIR 데이터를 안전 하 게 교환 하기 위해 의료 Api는 Azure API for FHIR에서 사용할 수 있는 몇 가지 증분 기능을 제공 합니다. 
* 트랜잭션 지원: 의료 Api에서 FHIR 서비스는 트랜잭션 번들을 지원 합니다. 트랜잭션 번들에 대 한 자세한 내용은 HL7.org를 방문 하 여 일괄 처리/트랜잭션 상호 작용을 참조 하세요.
* 연결 된 검색 기능 향상: 연결 된 검색 & 예약 체인 검색은 하위 쿼리당 100 항목으로 제한 되지 않습니다.


## <a name="next-steps"></a>다음 단계

Azure 의료 Api 작업을 시작 하려면 5 분 빠른 시작을 따라 작업 영역을 배포 합니다.

> [!div class="nextstepaction"]
> [Azure Portal에서 작업 영역 배포](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [작업 영역 개요](workspace-overview.md)
