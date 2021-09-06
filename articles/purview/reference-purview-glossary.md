---
title: Purview 제품 용어집
description: Azure Purview 전체에서 사용되는 용어를 정의하는 용어집
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 77c1d203808683d50e0a7648c686f84c7d23b354
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607918"
---
# <a name="azure-purview-product-glossary"></a>Azure Purview 제품 용어집

다음은 Azure Purview 전체에서 사용되는 용어의 용어집입니다.

## <a name="annotation"></a>Annotation
Azure Purview의 데이터 자산과 관련된 정보(예: 용어집 용어 및 분류). 주석이 적용된 후 검색 내에서 주석을 사용하여 데이터 자산의 검색을 지원합니다. 
## <a name="approved"></a>승인됨
요청 상태를 변경할 권한이 있는 지정된 개인 또는 그룹에서 만족할 수 있는 것으로 수락된 모든 요청에 지정된 상태입니다. 
## <a name="asset"></a>자산
Azure Purview 데이터 카탈로그 내에 저장된 단일 개체입니다.
> [!NOTE]
> 카탈로그의 단일 개체는 잠재적으로 스토리지의 많은 개체를 나타낼 수 있습니다. 예를 들어 리소스 집합은 자산이지만 스토리지의 많은 파티션 파일로 구성됩니다.
## <a name="azure-information-protection"></a>Azure Information Protection
정보를 분류하고 보호하기 위해 문서 및 이메일의 레이블 지정을 지원하는 클라우드 솔루션입니다. 레이블이 지정된 항목은 암호화로 보호되거나 워터마크로 표시되거나 특정 작업 또는 사용자로 제한될 수 있으며 항목에 바인딩됩니다. 이 클라우드 기반 솔루션은 Azure RMS(Rights Management Service)를 사용하여 제한을 적용합니다. 
## <a name="business-glossary"></a>비즈니스 용어집
조직에서 주요 비즈니스 단어 및 해당 정의를 설명하는 데 사용하는 특수 용어의 검색 가능한 목록입니다. 비즈니스 용어집을 사용하면 조직 전체에서 일관된 데이터 사용량을 제공할 수 있습니다. 
## <a name="classification-report"></a>분류 보고서
스캔한 데이터에 대한 주요 분류 세부 정보를 보여 주는 보고서입니다.  
## <a name="classification"></a>분류
자산의 특성 또는 열(예: "Age", "Email Address" 및 "Street Address")을 식별하는 데 사용되는 주석 유형입니다. 이러한 특성은 검사 중에 할당하거나 수동으로 추가할 수 있습니다. 
## <a name="classification-rule"></a>분류 규칙
분류 규칙은 콘텐츠가 지정된 패턴과 일치할 때 검사된 데이터를 분류하는 방법을 결정하는 조건 집합입니다.
## <a name="classified-asset"></a>분류 자산
자동 검사 중에 Azure Purview가 스키마를 추출하고 분류를 적용하는 자산입니다. 검사 규칙 집합은 어떤 자산이 분류되는지 결정합니다. 자산이 분류 후보로 간주되고 검사 시간 동안 분류가 적용되지 않는 경우에도 자산은 분류 자산으로 간주됩니다.
## <a name="column-pattern"></a>열 패턴
열 규칙에 포함되는 정규식으로, 일치시킬 열 이름을 나타냅니다.
## <a name="contact"></a>연락처
데이터 카탈로그의 엔터티와 연결된 개인 
## <a name="control-plane-operation"></a>컨트롤 플레인 작업
역할 기반 액세스 제어 및 Azure Policy와 같이 Azure Resource Manager 엔드포인트로 전송되는 구독의 리소스를 관리하는 작업입니다. 
## <a name="credential"></a>자격 증명
액세스 제어 시스템에서 사용되는 ID 또는 도구의 확인입니다. 자격 증명은 데이터 자산에 대한 액세스 권한을 부여하기 위해 개인 또는 그룹을 인증하는 데 사용할 수 있습니다. 
## <a name="data-catalog"></a>데이터 카탈로그
고객이 데이터 자산의 자산에 대한 메타데이터를 보고 관리할 수 있게 해주는 Azure Purview 기능입니다.
## <a name="data-map"></a>데이터 맵
고객이 데이터 자산을 관리할 수 있게 해주는 Azure Purview 기능입니다(예: 검색, 계보 및 이동).
## <a name="data-pattern"></a>데이터 패턴
데이터 필드에 저장된 데이터를 나타내는 정규식입니다. 예를 들어 직원 ID의 데이터 패턴은 Employee{GUID}일 수 있습니다.
## <a name="data-plane-operation"></a>데이터 평면 작업
자산 편집 또는 용어집 용어 만들기와 같은 특정 Azure Purview 인스턴스 내의 작업입니다. 각 인스턴스에는 사용자가 수행할 수 있는 데이터 평면 작업을 제어하는 ""데이터 판독기"" 및 ""데이터 레코더""와 같은 미리 정의된 역할이 있습니다.
## <a name="discovered-asset"></a>검색된 자산
검사 프로세스 중에 Azure Purview가 데이터 원본에서 식별하는 자산입니다. 검색된 자산 수에는 리소스 집합 그룹화 이전의 모든 파일 또는 테이블이 포함됩니다.
## <a name="distinct-match-threshold"></a>고유 일치 임계값
스캐너가 데이터 패턴을 실행하기 전에 열에서 찾아야 하는 고유 데이터 값의 총 수입니다. 예를 들어 직원 ID에 대해 8이라는 고유 일치 임계값을 사용하려면, 직원 ID에 대해 설정된 데이터 패턴과 일치하는 8개 이상의 고유 데이터 값이 열의 샘플링된 값 중에 있어야 합니다.
## <a name="expert"></a>전문가
데이터 자산 또는 용어집 용어의 전체 컨텍스트를 이해하는 조직 내의 개인입니다.
## <a name="full-scan"></a>전체 검사
데이터 원본의 선택 범위 내에서 모든 자산을 처리하는 검사입니다.
## <a name="fully-qualified-name-fqn"></a>FQN(정규화된 이름)
데이터 원본 내에서 자산의 위치를 정의하는 경로입니다.  
## <a name="glossary-term"></a>용어집 용어
조직에 특정한 개념을 정의하는 비즈니스 용어집의 항목입니다. 용어집 용어는 동의어, 머리글자어 및 관련 용어에 대한 정보를 포함할 수 있습니다.
## <a name="incremental-scan"></a>증분 검사
이전의 성공적인 검사 이후 생성, 수정 또는 삭제된 자산을 검색하고 처리하는 검사입니다. 증분 검사를 실행하려면 원본에서 한 번 이상의 전체 검사를 완료해야 합니다.
## <a name="ingested-asset"></a>수집된 자산
검사 및 분류(해당하는 경우)되어 Azure Purview 데이터 맵에 추가된 자산입니다. 수집된 자산은 자동 검사 또는 외부 연결(예: Azure Data Factory 및 Azure Synapse)을 통해 데이터 카탈로그 내에서 검색 및 사용이 가능합니다.
## <a name="insights"></a>자세한 정보
데이터에 대한 정보를 요약하는 보고서를 볼 수 있는 Azure Purview 내의 영역입니다.
## <a name="integration-runtime"></a>통합 런타임
데이터 원본에서 검색에 사용하는 컴퓨팅 인프라입니다.
## <a name="lineage"></a>계보
데이터가 원점에서 대상으로 이동할 때 데이터가 변환되고 흐르는 방식입니다. 데이터 자산 전체에서 이러한 흐름을 이해하면 조직에서 데이터 기록을 확인하고 문제 해결 또는 영향 분석에 도움이 됩니다. 
## <a name="management-center"></a>관리 센터
연결, 사용자, 역할 및 자격 증명을 관리할 수 있는 Azure Purview 내의 영역입니다.
## <a name="minimum-match-threshold"></a>최소 일치 임계값
분류를 적용하기 위해 스캐너로 검색해야 하는 열에서 일치하는 개별 데이터 값의 최소 백분율입니다. 

예를 들어 직원 ID에 대해 60%의 최소 일치 임계값을 사용하려면 열의 샘플링된 데이터 중에서 모든 고유 값의 60%가 직원 ID에 대해 설정된 데이터 패턴과 일치해야 합니다. 스캐너가 열에서 128개 값을 샘플링하고 해당 열에서 60개의 고유 값을 찾은 경우, 분류를 적용하려면 고유 값 중 36개 이상(60%)이 직원 ID 데이터 패턴과 일치해야 합니다.
## <a name="on-premises-data"></a>온-프레미스 데이터
예를 들어 클라우드 또는 SaaS(Software as a Service)가 아닌 고객이 제어하는 데이터 센터에 있는 데이터입니다. 
## <a name="owner"></a>소유자
데이터 자산 관리를 담당하는 개인 또는 그룹입니다.
## <a name="pattern-rule"></a>패턴 규칙
Azure Purview가 자산을 리소스 집합으로 그룹화하고 카탈로그 내에 표시하는 방법을 재정의하는 구성입니다.
## <a name="purview-instance"></a>Purview 인스턴스
단일 Azure Purview 리소스입니다. 
## <a name="registered-source"></a>등록된 원본
Azure Purview 인스턴스에 추가되었으며 이제 데이터 카탈로그의 일부로 관리되는 원본입니다. 
## <a name="related-terms"></a>관련 용어
조직 내의 다른 용어와 연결된 용어집 용어입니다.  
## <a name="resource-set"></a>리소스 집합
스토리지의 여러 분할된 파일 또는 개체를 나타내는 단일 자산입니다. 예를 들어 Azure Purview는 분할된 Apache Spark 출력을 각 개별 파일에 대한 고유한 자산 대신 단일 리소스 집합으로 저장합니다. 
## <a name="role"></a>역할
Azure Purview 인스턴스 내의 사용자에게 할당된 권한입니다. Purview 데이터 큐레이터 또는 Purview 데이터 읽기 권한자와 같은 역할은 제품 내에서 수행할 수 있는 작업을 결정합니다.
## <a name="scan"></a>검사
원본 또는 원본 집합을 검사하고 해당 메타데이터를 데이터 카탈로그에 수집하는 Azure Purview 프로세스입니다. 스캔 트리거를 사용하여 수동으로 또는 일정에 따라 검사를 실행할 수 있습니다. 
## <a name="scan-ruleset"></a>검사 규칙 집합
검색이 카탈로그에 수집되는 데이터 형식 및 분류를 정의하는 규칙 집합입니다. 
## <a name="scan-trigger"></a>검사 트리거
검색이 실행되는 경우의 되풀이를 결정하는 일정입니다.
## <a name="search"></a>검색
키워드와 일치하는 자산 목록을 반환하는 Azure Purview의 데이터 검색 기능입니다. 
## <a name="search-relevance"></a>검색 관련성
검색 결과가 반환되는 순서를 결정하는 데이터 자산의 채점입니다. 여러 요인에 따라 자산의 관련성 점수가 결정됩니다.
## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임
온-프레미스 또는 개인 네트워크의 데이터에 연결하는 데 사용되는 개인 네트워크 내의 온-프레미스 머신 또는 가상 머신에 설치된 통합 런타임입니다.
## <a name="sensitivity-label"></a>민감도 레이블
조직의 데이터를 분류하고 보호하는 주석입니다. Azure Purview는 민감도 레이블을 만들기 위해 Microsoft Information Protection과 통합됩니다. 
## <a name="sensitivity-label-report"></a>민감도 레이블 보고서
데이터 자산에 적용되는 민감도 레이블에 대한 요약입니다. 
## <a name="service"></a>서비스
독립 실행형 기능을 제공하고 구독 또는 라이선스를 통해 고객에게 제공되는 제품입니다. 
## <a name="source"></a>원본
데이터가 저장되는 시스템입니다. 원본은 클라우드 또는 온-프레미스와 같은 다양한 위치에 호스트될 수 있습니다. Azure Purview에서 관리할 수 있도록 원본을 등록하고 검사합니다. 
## <a name="source-type"></a>소스 형식
Azure Purview 인스턴스에서 사용되는 등록된 원본의 분류입니다(예: Azure SQL Database, Azure Blob Storage, Amazon S3 또는 SAP ECC). 
## <a name="steward"></a>관리자
용어집 용어에 대한 표준을 정의하는 개인입니다. 할당된 엔터티에 대한 품질 표준, 명명법 및 규칙을 유지 관리해야 합니다. 
## <a name="term-template"></a>용어 템플릿
용어집 용어에 포함된 특성의 정의입니다. 사용자는 시스템 정의 용어 템플릿을 사용하거나 사용자 지정 특성을 포함하는 자체 템플릿을 만들 수 있습니다.
## <a name="next-steps"></a>다음 단계

Azure Purview를 시작하려면 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.