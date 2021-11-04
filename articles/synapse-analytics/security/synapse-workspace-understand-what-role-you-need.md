---
title: Azure Synapse 일반 작업을 수행하는 데 필요한 역할 이해
description: 이 문서에서는 특정 작업을 수행하는 데 필요한 기본 제공 Synapse RBAC 역할에 대해 설명합니다.
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/02/2021
ms.author: mesrivas
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 376c91c93c61f3070c5b81a24fd8ae145bdee6ad
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018834"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-azure-synapse"></a>Azure Synapse 일반 작업을 수행하는 데 필요한 역할 이해

이 문서는 Synapse Studio 작업을 수행하는 데 필요한 Synapse RBAC(역할 기반 액세스 제어) 역할 또는 Azure RBAC 역할을 이해하는 데 도움이 됩니다.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>Synapse Studio 액세스 제어 및 워크플로 요약 

### <a name="access-synapse-studio"></a>액세스 Synapse Studio

Synapse RBAC 역할이 할당되었거나 작업 영역에 대한 Azure 소유자, 기여자 또는 읽기 권한자 역할이 있는 경우 Synapse Studio를 열어 작업 영역의 세부 정보를 보고 해당 Azure 리소스(SQL 풀, Spark 풀 또는 통합 런타임)를 나열할 수 있습니다.

### <a name="resource-management"></a>리소스 관리

작업 영역에서 Azure 소유자 또는 기여자인 경우 SQL 풀, 데이터 탐색기 풀, Apache Spark 풀 및 통합 런타임을 만들 수 있습니다. 자동화된 배포에 ARM 템플릿을 사용하는 경우 리소스 그룹에서 Azure 기여자이어야 합니다.

작업 영역 또는 해당 리소스에 대한 Azure 소유자 또는 기여자인 경우 전용 SQL 풀을 일시 중지하거나 크기를 조정하고, Spark 풀 또는 통합 런타임을 구성할 수 있습니다.

### <a name="view-and-edit-code-artifacts"></a>코드 아티팩트 보기 및 편집

Synapse Studio 대한 액세스 권한을 사용하여 SQL 스크립트, KQL 스크립트, Notebook, Spark 작업, 연결된 서비스, 파이프라인, 데이터 흐름, 트리거 및 자격 증명과 같은 새 코드 아티팩트 를 만들 수 있습니다.  (이러한 아티팩트는 추가 권한으로 게시 또는 저장할 수 있습니다.)  

Synapse 아티팩트 사용자, Synapse 아티팩트 게시자, Synapse 기여자 또는 Synapse 관리자는 이미 게시된 코드 아티팩트를 나열하고, 열고, 편집할 수 있습니다.

### <a name="execute-your-code"></a>코드 실행

SQL 풀에 필요한 SQL 권한이 정의되어 있는 경우 SQL 풀에서 SQL 스크립트를 실행할 수 있습니다. 필요한 권한이 있는 경우 데이터 탐색기 풀에서 KQL 스크립트를 실행할 수 있습니다.  

작업 영역 또는 특정 Apache Spark 풀에서 Synapse 컴퓨팅 운영자 권한이 있는 경우 Notebook 및 Spark 작업을 실행할 수 있습니다.  

작업 영역 또는 특정 통합 런타임에 대한 컴퓨팅 운영자 권한과 적절한 자격 증명 권한을 사용하여 파이프라인을 실행할 수 있습니다.

### <a name="monitor-and-manage-execution"></a>실행 모니터링 및 관리

Synapse 사용자인 경우 Apache Spark 풀에서 실행 중인 Notebook 및 작업의 상태를 검토할 수 있습니다.

작업 영역에서 또는 특정 Spark 풀 또는 파이프라인에 대한 Synapse 컴퓨팅 운영자인 경우 로그를 검토하고 실행 중인 작업 및 파이프라인을 취소할 수 있습니다.  

### <a name="publish-and-save-your-code"></a>코드 게시 및 저장

Synapse 아티팩트 게시자, Synapse 기여자 또는 Synapse 관리자인 경우 서비스에 새 코드 아티팩트 또는 업데이트된 코드 아티팩트를 게시할 수 있습니다. 

작업 영역에서 Git를 사용할 수 있고 Git 권한이 있는 경우 Git 리포지토리의 작업 분기에 코드 아티팩트를 커밋할 수 있습니다. Git를 사용하는 경우에는 공동 작업 분기에서만 게시를 수행할 수 있습니다.

코드 아티팩트에 대한 변경 내용을 게시 또는 커밋하지 않고 Synapse Studio를 닫으면 변경 내용이 손실됩니다.


## <a name="tasks-and-required-roles"></a>작업 및 필수 역할

다음 표에는 일반적인 작업 및 각 작업에 필요한 Synapse RBAC 또는 Azure RBAC 역할이 나와 있습니다.  

>[!Note]
> Synapse 관리자는 필요한 권한을 제공하는 유일한 역할이 아닌 한 각 작업에 나열되지 않습니다. Synapse 관리자는 다른 Synapse RBAC 역할에 설정된 모든 작업을 수행할 수 있습니다.</br>

필요한 최소 Synapse RBAC 역할이 표시됩니다. 

모든 범위의 모든 Synapse RBAC 역할은 작업 영역에서 Synapse 사용자 권한을 제공합니다.

표에 표시된 모든 Synapse RBAC 권한/작업에는 접두사 가 `Microsoft/Synapse/workspaces/...` 갖습니다.


수행할 작업 |필요한 역할|Synapse RBAC 권한/작업
--|--|--
|작업 영역에서 Synapse Studio 열기|Synapse 사용자 또는|읽기
| |작업 영역에 대한 Azure 소유자, 기여자 또는 읽기 권한자|없음
|SQL 풀, 데이터 탐색기 풀, Apache Spark 풀, 통합 런타임을 나열하고 해당 구성 세부 정보 액세스|Synapse 사용자 또는|읽기|
||작업 영역에 대한 Azure 소유자, 기여자 또는 읽기 권한자|없음
|연결된 서비스, 자격 증명, 관리형 프라이빗 엔드포인트 나열|Synapse 사용자|읽기
SQL 풀|
전용 SQL 풀 또는 서버리스 SQL 풀 만들기|작업 영역에 대한 Azure 소유자 또는 기여자|없음
전용 SQL 풀 관리(일시 중지, 크기 조정 또는 삭제)|SQL 풀 또는 작업 영역에 대한 Azure 소유자 또는 기여자|없음
SQL 스크립트 만들기</br>|Synapse 사용자 또는 </br>작업 영역에 대한 Azure 소유자 또는 기여자 </br>SQL 스크립트를 실행하거나, 게시하거나, 변경 내용을 커밋하려면 추가 SQL 권한이 필요합니다.|
게시된 SQL 스크립트 나열 및 열기| Synapse 아티팩트 사용자, 아티팩트 게시자, Synapse 기여자|artifacts/read
서버리스 SQL 풀에서 SQL 스크립트 실행|풀에 대한 SQL 권한(Synapse 관리자에게 자동으로 부여됨)|없음
전용 SQL 풀에서 SQL 스크립트 실행|풀에 대한 SQL 권한(Synapse 관리자에게 자동으로 부여됨)|없음
새, 업데이트된 또는 삭제된 SQL 스크립트 게시|Synapse 아티팩트 게시자, Synapse 기여자|sqlScripts/write, delete
SQL 스크립트에 대한 변경 내용을 Git 리포지토리에 커밋|리포지토리에 대한 Git 권한이 필요|
작업 영역에 Active Directory 관리자 할당(Azure Portal의 작업 영역 속성을 통해)|작업 영역에 대한 Azure 소유자 또는 기여자 |
데이터 탐색기 풀|
데이터 탐색기 풀 만들기 |작업 영역에 대한 Azure 소유자 또는 기여자|없음
데이터 탐색기 풀 관리(일시 중지, 크기 조정 또는 삭제)|데이터 탐색기 풀 또는 작업 영역의 Azure 소유자 또는 기여자|없음
KQL 스크립트 만들기</br>|Synapse User, </br>*스크립트를 실행하거나, 변경 내용을 게시하거나 커밋하려면 추가 데이터 탐색기 권한이 필요합니다.*|
게시된 KQL 스크립트 나열 및 열기| Synapse 아티팩트 사용자, 아티팩트 게시자, Synapse 기여자|artifacts/read
데이터 탐색기 풀에서 KQL 스크립트 실행| 풀에 대한 권한 데이터 탐색기(Synapse 관리자에게 자동으로 부여)|없음
KQL 스크립트 새로 게시, 업데이트 또는 삭제|Synapse 아티팩트 게시자, Synapse 기여자|kqlScripts/write, delete
KQL 스크립트의 변경 내용을 Git 리포지토에 커밋|리포지토리에 대한 Git 권한이 필요|
Apache Spark 풀|
Apache Spark 풀 만들기|작업 영역에 대한 Azure 소유자 또는 기여자|
Apache Spark 애플리케이션 모니터링| Synapse 사용자|읽기
Notebook 및 작업 실행 로그 보기 |Synapse 컴퓨팅 운영자|
Apache Spark 풀에서 실행 중인 모든 Notebook 또는 Spark 작업 취소|Apache Spark 풀의 Synapse 컴퓨팅 연산자.|bigDataPools/useCompute
Notebook 또는 작업 정의 만들기|Synapse 사용자 또는 </br>작업 영역에 대한 Azure 소유자, 기여자 또는 읽기 권한자</br> 실행하거나, 게시하거나, 변경 내용을 커밋하려면 추가 권한이 필요합니다.|읽기</br></br></br></br></br> 
게시된 Notebook 또는 작업 정의를 나열 및 열기(저장된 출력에 대한 검토 포함)|작업 영역에 대한 Synapse 아티팩트 사용자, Synapse 아티팩트 게시자, Synapse 기여자|artifacts/read
Notebook 실행 및 해당 출력 검토 또는 Spark 작업 제출|선택한 Apache Spark 풀의 Synapse Apache Spark 관리자, Synapse 컴퓨팅 운영자|bigDataPools/useCompute 
Notebook 또는 작업 정의(출력 포함)를 서비스에 게시 또는 삭제|작업 영역에 대한 아티팩트 게시자, Synapse Apache Spark 관리자|notebooks/write, delete
Notebook 또는 작업 정의에 대한 변경 내용을 Git 리포지토리에 커밋|Git 권한|없음
파이프라인, 통합 런타임, 데이터 흐름, 데이터 세트 및 트리거|
통합 런타임 만들기, 업데이트 또는 삭제|작업 영역에 대한 Azure 소유자 또는 기여자|
통합 런타임 상태 모니터링|Synapse 컴퓨팅 운영자|read, integrationRuntimes/viewLogs
파이프라인 실행 검토|Synapse 아티팩트 게시자/Synapse 기여자|read, pipelines/viewOutputs 
파이프라인 만들기 |Synapse 사용자</br>디버그하거나, 트리거를 추가하거나, 게시하거나, 변경 내용을 커밋하려면 추가 Synapse 권한이 필요합니다.|읽기
데이터 흐름 또는 데이터 세트 만들기 |Synapse 사용자</br>게시하거나 변경 내용을 커밋하려면 추가 Synapse 권한이 필요합니다.|읽기
게시된 파이프라인 나열 및 열기 |Synapse 아티팩트 사용자 | artifacts/read
데이터 세트 데이터 미리 보기|Synapse 사용자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID)| 
기본 통합 런타임을 사용하여 파이프라인 디버그|Synapse 사용자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID 자격 증명)|read, </br>credentials/useSecret
지금 트리거를 포함하여 트리거 만들기(파이프라인을 실행할 수 있는 권한 필요)|Synapse 사용자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID)|read, credentials/useSecret/action
파이프라인 실행|Synapse 사용자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID)|read, credentials/useSecret/action
데이터 복사 도구를 사용하여 데이터 복사|Synapse 사용자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID)|read, credentials/useSecret/action
데이터 수집(일정 사용)|Synapse 작성자 + Synapse 자격 증명 사용자(작업 영역 시스템 ID)|read, credentials/useSecret/action
새, 업데이트된 또는 삭제된 파이프라인, 데이터 흐름 또는 트리거를 서비스에 게시|작업 영역에 대한 Synapse 아티팩트 게시자|pipelines/write, delete</br>dataflows/write, delete</br>triggers/write, delete
파이프라인, 데이터 흐름, 데이터 세트 또는 트리거에 대한 변경 내용을 Git 리포지토리에 커밋 |Git 권한|없음 
연결된 서비스|
연결된 서비스 만들기(자격 증명 할당 포함)|Synapse 사용자</br>자격 증명으로 연결된 서비스를 사용하거나, 게시하거나, 변경 내용을 커밋하려면 추가 권한이 필요합니다.|읽기
게시된 연결된 서비스 나열 및 열기|Synapse 아티팩트 사용자|linkedServices/write, delete  
자격 증명으로 보호되는 연결된 서비스에 대한 연결 테스트|Synapse 사용자 + Synapse 자격 증명 사용자|credentials/useSecret/action|
연결된 서비스 게시|Synapse 아티팩트 게시자, Synapse 연결된 데이터 관리자|linkedServices/write, delete
연결된 서비스 정의를 Git 리포지토리에 커밋|Git 권한|없음
액세스 관리|
모든 범위에서 Synapse RBAC 역할 할당 검토|Synapse 사용자|읽기
사용자, 그룹 및 서비스 주체에 대한 Synapse RBAC 역할 할당 및 제거| 작업 영역 또는 특정 작업 영역 항목 범위에서 Synapse 관리자|roleAssignments/write, delete 

> [!Note]
> 다른 테넌트의 게스트 사용자도 Synapse 관리자로 할당되면 역할 할당을 검토, 추가 또는 변경할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

 - [Synapse RBAC 역할 할당 검토](./how-to-review-synapse-rbac-role-assignments.md)
 - [Synapse RBAC 역할 할당 관리](./how-to-manage-synapse-rbac-role-assignments.md)
