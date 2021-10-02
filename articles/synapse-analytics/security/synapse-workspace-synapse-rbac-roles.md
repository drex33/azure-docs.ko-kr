---
title: Synapse RBAC 역할
description: 이 문서는 기본 제공 Synapse RBAC 역할에 대해 설명합니다.
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 6b6c2cbf04b0e68f86bd11b8295c54b8c65d0360
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352788"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC 역할

이 문서는 기본 제공 Synapse RBAC 역할, 이 역할이 부여하는 권한, 이 역할을 사용할 수 있는 범위에 관해 설명합니다.  

## <a name="whats-changed-since-the-preview"></a>미리 보기 이후 변경된 내용
미리 보기 중에 제공된 Synapse RBAC 역할에 익숙한 사용자의 경우 다음 변경 내용이 적용됩니다.
- 작업 영역 관리자의 이름이 **Synapse Administrator** 로 바뀜
- Apache Spark 관리자는 **Synapse Apache Spark 관리자** 로 이름이 바뀌고 SQL 스크립트를 포함하여 게시된 모든 코드 아티팩트를 볼 수 있는 권한이 있습니다.  해당 역할은 더 이상 Synapse 자격 증명 사용자 역할을 필요로 하는 작업 영역 MSI를 사용할 수 있는 권한을 부여하지 않습니다.  파이프라인을 실행하려면 해당 권한이 필요합니다. 
- SQL 관리자는 **Synapse SQL 관리자** 로 이름이 바뀌었으며 Spark Notebooks 및 작업을 포함하여 게시된 모든 코드 아티팩트를 볼 수 있는 권한이 있습니다.  해당 역할은 더 이상 Synapse 자격 증명 사용자 역할을 필요로 하는 작업 영역 MSI를 사용할 수 있는 권한을 부여하지 않습니다. 파이프라인을 실행하려면 해당 권한이 필요합니다.
- 구체적 분석 런타임이 아닌 개발 및 운영 가상 사용자를 지원하는 데 중점을 둔 **새로운 세부화된 Synapse RBAC 역할** 이 도입되었습니다.  
- **새 하위 수준 범위** 는 여러 역할에 대하여 도입됩니다.  해당 범위를 통해 특정 리소스나 개체로 역할을 제한할 수 있습니다.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>기본 제공 Synapse RBAC 역할 및 범위

다음 표에서는 기본 제공 역할 및 해당 역할을 사용할 수 있는 범위에 대해 설명합니다.

>[!Note]
> 어떤 범위에서든 Synapse RBAC 역할을 가진 사용자는 작업 영역 범위의 Synapse 사용자 역할도 자동으로 포함합니다. 

> [!IMPORTANT]
> Synapse RBAC 역할은 Synapse 작업 영역에서 SQL 풀, Apache Spark 풀 및 통합 런타임을 만들거나 관리할 수 있는 권한을 부여 하지 않습니다. 이러한 작업을 수행 하려면 리소스 그룹에 대 한 azure 소유자 또는 Azure 참여자 역할이 필요 합니다.

|역할 |사용 권한|범위|
|---|---|-----|
|Synapse 관리자  |SQL 풀, Apache Spark 풀 및 통합 런타임에 대 한 전체 Synapse 액세스.  게시된 모든 코드 아티팩트에 대한 만들기, 읽기, 업데이트, 삭제 액세스를 포함합니다.  작업 영역 시스템 ID 자격 증명에 대한 컴퓨팅 운영자, 연결된 데이터 관리자, 자격 증명 사용자 권한을 포함합니다.  Synapse RBAC 역할 할당을 포함합니다. Synapse 관리자 외에도 Azure 소유자는 Synapse RBAC 역할을 할당할 수 있습니다. 컴퓨팅 리소스를 만들고, 삭제하고, 관리하려면 Azure 권한이 필요합니다. </br></br>아티팩트를 읽고 쓸 수 있습니다.</br> Spark 작업에 대한 모든 동작을 할 수 있습니다.</br> Spark 풀 로그를 볼 수 있습니다.</br> 저장된 Notebooks 및 파이프라인 출력을 볼 수 있습니다. </br> 연결된 서비스 또는 자격 증명에서 저장한 비밀을 사용할 수 있습니다.</br>현재 범위에서 Synapse RBAC 역할을 할당하고 철회할 수 있습니다.|작업 영역 </br> Spark 풀<br/>통합 런타임 </br>연결된 서비스</br>자격 증명 |
|Synapse Apache Spark 관리자</br>|Apache Spark 풀에 대한 전체 Synapse 액세스입니다.  게시된 Spark 작업 정의, Notebooks 및 해당 출력, 라이브러리, 연결된 서비스, 자격 증명에 대한 액세스를 만들고, 읽고, 업데이트하고, 삭제합니다.  다른 모든 게시된 코드 아티팩트에 대한 읽기 권한을 포함합니다. 자격 증명을 사용하고 파이프라인을 실행할 수 있는 권한을 포함하지 않습니다. 액세스 부여는 포함하지 않습니다. </br></br>_Spark 아티팩트의 모든 작업을 수행할 수 있습니다</br>Spark 작업에 대한 모든 작업을 수행할 수 있습니다_|작업 영역</br>Spark 풀|
|Synapse SQL 관리자|서버리스 SQL 풀에 대한 전체 Synapse 액세스입니다.  게시된 SQL 스크립트, 자격 증명, 연결된 서비스에 대한 액세스를 만들고, 읽고, 업데이트하고, 삭제합니다.  다른 모든 게시된 코드 아티팩트에 대한 읽기 권한을 포함합니다.  자격 증명을 사용하고 파이프라인을 실행할 수 있는 권한을 포함하지 않습니다. 액세스 부여는 포함하지 않습니다. </br></br>*SQL 스크립트에 대한 모든 작업을 수행할 수 있습니다<br/>SQL로 SQL 서버리스 엔드포인트에 연결할 수 있습니다 `db_datareader`, `db_datawriter`, `connect`, `grant` 권한*|작업 영역|
|Synapse Contributor|Apache Spark 풀 및 통합 런타임에 대한 전체 Synapse 액세스입니다. 자격 증명 및 연결된 서비스를 비롯하여 게시된 모든 코드 아티팩트와 해당 출력에 대한 액세스를 만들고, 읽고, 업데이트하고 삭제하는 것이 포함됩니다.  컴퓨팅 운영자 권한을 포함합니다. 자격 증명을 사용하고 파이프라인을 실행할 수 있는 권한을 포함하지 않습니다. 액세스 부여는 포함하지 않습니다. </br></br>_아티팩트를 읽고 쓸 수 있습니다</br>저장된 Notebooks 및 파이프라인 출력을 볼 수 있습니다</br>Spark 작업에 대한 모든 작업을 수행할 수 있습니다</br>Spark 풀 로그를 볼 수 있습니다_|작업 영역 </br> Spark 풀<br/> 통합 런타임|
|Synapse 아티팩트 게시자|게시된 코드 아티팩트와 해당 출력에 대한 액세스를 만들고, 읽고, 업데이트하고 삭제합니다. 코드 또는 파이프라인을 실행하거나 액세스 권한을 부여할 수 있는 권한은 포함되지 않습니다. </br></br>_게시된 아티팩트를 읽고 아티팩트를 게시할 수 있습니다</br>저장된 Notebooks, Spark 작업, 파이프라인 출력을 볼 수 있습니다_|작업 영역
|Synapse 아티팩트 사용자|게시된 코드 아티팩트 및 해당 출력에 대한 읽기 액세스입니다. 새 아티팩트를 만들 수 있지만 추가 권한 없이는 변경 내용을 게시하거나 코드를 실행할 수 없습니다.|작업 영역
|Synapse 컴퓨팅 운영자 |Spark 작업 및 Notebooks를 제출하고 로그를 봅니다.  사용자가 제출한 Spark 작업 취소를 포함합니다. 파이프라인을 실행하고 파이프라인 실행 및 출력을 보기 위해 작업 영역 시스템 ID에 대한 추가 자격 증명 사용 권한이 필요합니다. </br></br>_다른 사용자가 제출한 작업을 포함하여 작업을 제출하고 취소할 수 있습니다</br>Spark 풀 로그를 볼 수 있습니다_|작업 영역</br>Spark 풀</br>통합 런타임|
|Synapse 자격 증명 사용자|파이프라인 실행과 같은 활동에서 자격 증명 및 연결된 서비스 내 비밀의 런타임 및 구성 시간 사용입니다. 파이프라인을 실행하려면 작업 영역 시스템 ID로 범위가 지정된 해당 역할이 필요합니다. </br></br>_자격 증명으로 범위가 지정되어 자격 증명으로 보호되는 연결된 서비스를 통해 데이터에 액세스할 수 있도록 허용합니다(컴퓨팅 사용 권한도 필요) </br>작업 영역 시스템 ID 자격 증명으로 보호되는 파이프라인(추가 컴퓨팅 사용 권한 포함)을 실행할 수 있습니다_|작업 영역 </br>연결된 서비스</br>자격 증명
|Synapse 연결된 데이터 관리자|관리형 프라이빗 엔드포인트, 연결된 서비스, 자격 증명을 만들고 관리합니다. 자격 증명으로 보호되고 연결된 서비스를 사용하는 관리형 프라이빗 엔드포인트를 만들 수 있습니다.|작업 영역|
|Synapse 사용자|SQL 풀, Apache Spark 풀, 통합 런타임, 게시되고 연결된 서비스와 자격 증명에 대한 세부 정보를 나열하고 표시합니다. 다른 게시된 코드 아티팩트를 포함하지 않습니다.  새 아티팩트를 만들 수 있지만 추가 권한 없이 실행하거나 게시할 수 없습니다.</br></br>_Spark 풀, 통합 런타임을 나열하고 읽을 수 있습니다._|작업 영역, Spark 풀</br>연결된 서비스 </br>자격 증명|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC 역할 및 해당 역할에서 허용하는 작업

>[!Note]
>- 아래 표에 나열된 모든 작업에는 “Microsoft.Synapse/...”라는 접두사가 붙습니다.</br>
>- 모든 아티팩트 읽기, 쓰기, 삭제 작업은 라이브 서비스의 게시된 아티팩트와 관련이 있습니다.  해당 권한은 연결된 Git 리포지토리의 아티팩트에 대한 액세스에 영향을 주지 않습니다.  

다음 표에서는 기본 제공 역할 및 각 역할이 지원하는 작업/권한을 보여 줍니다.

역할|동작
--|--
Synapse 관리자|workspaces/read</br>workspaces/roleAssignments/write, delete</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse Apache Spark 관리자|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/notebooks/viewOutputs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse SQL 관리자|workspaces/read</br>workspaces/artifacts/read</br>workspaces/sqlScripts/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse Contributor|workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 아티팩트 게시자|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/write, delete</br>workspaces/sparkJobDefinitions/write, delete</br>workspaces/sqlScripts/write, delete</br>workspaces/dataFlows/write, delete</br>workspaces/pipelines/write, delete</br>workspaces/triggers/write, delete</br>workspaces/datasets/write, delete</br>workspaces/libraries/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 아티팩트 사용자|workspaces/read</br>workspaces/artifacts/read</br>workspaces/notebooks/viewOutputs/action</br>workspaces/pipelines/viewOutputs/action|
|Synapse 컴퓨팅 운영자 |workspaces/read</br>workspaces/bigDataPools/useCompute/action</br>workspaces/bigDataPools/viewLogs/action</br>workspaces/integrationRuntimes/useCompute/action</br>workspaces/integrationRuntimes/viewLogs/action|
|Synapse 자격 증명 사용자|workspaces/read</br>workspaces/linkedServices/useSecret/action</br>workspaces/credentials/useSecret/action|
|Synapse 연결된 데이터 관리자|workspaces/read</br>workspaces/managedPrivateEndpoint/write, delete</br>workspaces/linkedServices/write, delete</br>workspaces/credentials/write, delete|
|Synapse 사용자|workspaces/read|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC 작업 및 해당 작업을 허용하는 역할

다음 표는 Synapse 작업 및 해당 작업을 허용하는 기본 제공 역할을 보여 줍니다.

작업|역할
--|--
workspaces/read|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자</br>Synapse 컴퓨팅 운영자 </br>Synapse 자격 증명 사용자</br>Synapse 연결된 데이터 관리자</br>Synapse 사용자 
workspaces/roleAssignments/write, delete|Synapse 관리자
workspaces/managedPrivateEndpoint/write, delete|Synapse 관리자</br>Synapse 연결된 데이터 관리자
workspaces/bigDataPools/useCompute/action|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 컴퓨팅 운영자 
workspaces/bigDataPools/viewLogs/action|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 컴퓨팅 운영자 
workspaces/integrationRuntimes/useCompute/action|Synapse 관리자</br>Synapse Contributor</br>Synapse 컴퓨팅 운영자
workspaces/integrationRuntimes/viewLogs/action|Synapse 관리자</br>Synapse Contributor</br>Synapse 컴퓨팅 운영자
workspaces/artifacts/read|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
workspaces/notebooks/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/sparkJobDefinitions/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/sqlScripts/write, delete|Synapse 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/dataFlows/write, delete|Synapse 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/pipelines/write, delete|Synapse 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/triggers/write, delete|Synapse 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/datasets/write, delete|Synapse 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/libraries/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자
workspaces/linkedServices/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 연결된 데이터 관리자
workspaces/credentials/write, delete|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 연결된 데이터 관리자
workspaces/notebooks/viewOutputs/action|Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
workspaces/pipelines/viewOutputs/action|Synapse 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자
workspaces/linkedServices/useSecret/action|Synapse 관리자</br>Synapse 자격 증명 사용자
workspaces/credentials/useSecret/action|Synapse 관리자</br>Synapse 자격 증명 사용자

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC 범위 및 지원되는 역할

아래 표는 Synapse RBAC 범위와 각 범위에서 할당할 수 있는 역할을 나열합니다. 

>[!note]
>개체를 만들거나 삭제하려면 더 높은 수준 범위의 권한이 있어야 합니다.

범위|역할
--|--
작업 영역 |Synapse 관리자</br>Synapse Apache Spark 관리자</br>Synapse SQL 관리자</br>Synapse Contributor</br>Synapse 아티팩트 게시자</br>Synapse 아티팩트 사용자</br>Synapse 컴퓨팅 운영자 </br>Synapse 자격 증명 사용자</br>Synapse 연결된 데이터 관리자</br>Synapse 사용자
Apache Spark 풀 | Synapse 관리자 </br>Synapse Contributor </br> Synapse 컴퓨팅 운영자
통합 런타임 | Synapse 관리자 </br>Synapse Contributor </br> Synapse 컴퓨팅 운영자
연결된 서비스 |Synapse 관리자 </br>Synapse 자격 증명 사용자
자격 증명 |Synapse 관리자 </br>Synapse 자격 증명 사용자
 
>[!note]
>모든 아티팩트 역할 및 작업은 작업 영역 수준에서 범위가 지정됩니다. 

## <a name="next-steps"></a>다음 단계

작업 영역에 대한 [Synapse RBAC 역할 할당을 검토하는 방법](./how-to-review-synapse-rbac-role-assignments.md)을 알아봅니다.

[Synapse RBAC 역할을 할당하는 방법](./how-to-manage-synapse-rbac-role-assignments.md)을 알아봅니다.
