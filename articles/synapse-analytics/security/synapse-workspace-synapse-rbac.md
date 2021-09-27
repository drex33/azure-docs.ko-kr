---
title: Synapse 역할 기반 액세스 제어
description: Azure Synapse Analytics의 역할 기반 액세스 제어를 설명하는 문서
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: e2a49fb9de3c4f63edc483724769c5c726d3a63c
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081855"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Synapse 역할 기반 액세스 제어(RBAC)란?

Synapse RBAC는 Synapse 작업 영역 및 해당 콘텐츠에 대한 [Azure RBAC](../../role-based-access-control/overview.md)의 기능을 확장합니다. 

Azure RBAC는 Synapse 작업 영역 및 해당 SQL 풀, Apache Spark 풀, 통합 런타임을 만들고 업데이트하거나 삭제할 수 있는 사용자를 관리하는 데 사용됩니다.

Synapse RBAC는 다음을 수행할 수 있는 사용자를 관리하는 데 사용됩니다.
- 코드 아티팩트를 게시하고 게시된 코드 아티팩트를 나열하거나 액세스합니다. 
- Apaches Spark 풀 및 통합 런타임에서 코드를 실행합니다.
- 자격 증명으로 보호되는 연결된(데이터) 서비스에 액세스합니다. 
- 작업 실행을 모니터링하거나 취소하고, 작업 출력 및 실행 로그를 검토합니다.  

>[!Note]
>Synapse RBAC는 게시 된 SQL 스크립트에 대 한 액세스를 관리 하는 데 사용 되지만 서버 리스와 전용 SQL 풀에는 제한 된 액세스 권한만 제공 합니다. SQL 풀에 대한 액세스는 주로 SQL 보안을 사용하여 제어됩니다.

## <a name="what-can-i-do-with-synapse-rbac"></a>Azure RBAC로 무엇을 할 수 있습니까?

Synapse RBAC로 다음과 같은 작업을 수행할 수 있습니다.
  - 사용자가 Apache Spark Notebook 및 작업에 대한 변경을 라이브 서비스로 게시하도록 허용합니다.
  - 사용자가 특정 Apache Spark 풀에서 Notebook 및 Spark 작업을 실행 및 취소하도록 허용합니다.
  - 사용자가 작업 영역 시스템 ID로 보호되는 파이프라인을 실행하고 자격 증명으로 보호되는 연결된 서비스의 데이터에 액세스할 수 있도록 특정 자격 증명의 사용을 허용합니다. 
  - 관리자가 특정 Spark 풀에서 작업 실행을 관리, 모니터링 및 취소하도록 허용합니다.    

## <a name="how-synapse-rbac-works"></a>Synapse RBAC 작업 방식
Azure RBAC와 마찬가지로 Synapse RBAC는 역할 할당을 만들어 작동합니다. 역할 할당은 보안 주체, 역할 정의, 범위라는 세 가지 요소로 구성됩니다.  

### <a name="security-principals"></a>보안 주체

_보안 주체_ 는 사용자, 그룹, 서비스 주체 또는 관리 ID입니다.

### <a name="roles"></a>역할
 
_역할_ 은 특정 리소스 종류 또는 아티팩트 형식에 대해 수행할 수 있는 권한 또는 작업의 컬렉션입니다.

Synapse는 여러 가상 사용자의 요구 사항과 일치하는 작업의 컬렉션을 정의하는 기본 제공 역할을 제공합니다.
- 관리자는 작업 영역을 만들고 구성하는 모든 권한을 가져올 수 있습니다. 
- 개발자는 SQL 스크립트, Notebook, 파이프라인 및 데이터 흐름을 만들고 업데이트하고 디버그할 수 있지만 프로덕션 컴퓨팅 리소스/데이터에는서 이 코드를 게시하거나 실행할 수 없습니다.
- 운영자는 코드 또는 실행 출력에 액세스하지 않고 시스템 상태, 애플리케이션 실행을 모니터링 및 관리하고 로그를 검토할 수 있습니다.
- 보안 인력은 코드, 컴퓨팅 리소스 또는 데이터에 액세스할 필요 없이 엔드포인트를 관리 및 구성할 수 있습니다.

기본 제공 Synapse 역할에 대해 [자세히 알아보세요](./synapse-workspace-synapse-rbac-roles.md). 

### <a name="scopes"></a>범위

_범위_ 는 액세스가 적용되는 리소스 또는 아티팩트를 정의합니다.  Synapse는 계층형 범위를 지원합니다.  상위 수준 범위에서 부여된 권한은 하위 수준의 개체에 상속됩니다.  Synapse RBAC에서 최상위 범위는 작업 영역입니다.  작업 영역 범위에 역할을 할당하면 작업 영역에서 적용 가능한 모든 개체에 권한을 부여합니다.  

작업 영역 내에서 현재 지원되는 범위는 Apache Spark 풀, Integration runtime, 연결된 서비스 및 자격 증명입니다. 

작업 영역 범위에서 코드 아티팩트에 대한 액세스 권한이 부여됩니다.  작업 영역 내에서 아티팩트 컬렉션에 대한 액세스 권한 부여는 이후 릴리스에서 지원될 예정입니다.

## <a name="resolving-role-assignments-to-determine-permissions"></a>역할 할당을 확인하여 권한 확인

역할 할당은 지정된 범위에서 역할에 의해 정의된 권한을 보안 주체에 부여합니다.

Synapse RBAC는 Azure RBAC와 같은 추가 모델입니다. 여러 역할을 단일 보안 주체 및 여러 범위에 할당할 수 있습니다. 보안 주체의 권한을 계산할 때 시스템은 보안 주체와 보안 주체를 직접적 또는 간접적으로 포함하는 그룹에 할당된 모든 역할을 고려합니다.  또한 적용되는 권한을 결정할 때 각 할당의 범위도 고려합니다.  

## <a name="enforcing-assigned-permissions"></a>할당된 권한 적용

Synapse Studio에서는 특정 단추가 옵션이 회색으로 표시되거나 필요한 권한이 없는 경우 작업을 시도하면 권한 오류가 반환될 수 있습니다. 

단추나 옵션을 사용하지 않도록 설정하면 단추나 옵션을 마우스로 가리키면 도구 설명에 필요한 권한이 표시됩니다.  Synapse 관리자에게 문의하여 필요한 권한을 부여하는 역할을 할당합니다. [여기](./synapse-workspace-synapse-rbac-roles.md)에서 특정 작업을 제공하는 역할을 확인할 수 있습니다.

## <a name="who-can-assign-synapse-rbac-roles"></a>누가 Synapse RBAC 역할을 할당할 수 있습니까?

Synapse 관리자만 Synapse RBAC 역할을 할당할 수 있습니다.  작업 영역 수준의 Synapse 관리자는 모든 범위에서 액세스 권한을 부여할 수 있습니다.  하위 수준 범위의 Synapse 관리자는 해당 범위에서만 액세스 권한을 부여할 수 있습니다. 

새 작업 영역을 만들면 작업 영역 범위에서 작성자에게 자동으로 Synapse 관리자 역할이 부여됩니다.   

## <a name="where-do-i-manage-synapse-rbac"></a>Synapse RBAC는 어디에서 관리합니까?

Synapse RBAC는 관리 허브의 액세스 제어 도구를 사용하여 Synapse Studio 내에서 관리됩니다. 

## <a name="next-steps"></a>다음 단계

기본 제공 [Synapse RBAC 역할](./synapse-workspace-synapse-rbac-roles.md)을 이해합니다.

작업 영역에 대한 [Synapse RBAC 역할 할당을 검토하는 방법](./how-to-review-synapse-rbac-role-assignments.md)을 알아봅니다.

[Synapse RBAC 역할을 할당하는 방법](./how-to-manage-synapse-rbac-role-assignments.md)을 알아봅니다.