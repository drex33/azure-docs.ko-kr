---
title: Azure Synapse 작업 영역 액세스 제어 개요
description: 이 문서에서는 Synapse 작업 영역에 대한 액세스를 제어하는 데 사용되는 메커니즘과 그 안에 포함된 리소스 및 코드 아티팩트에 대해 설명합니다.
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 11/02/2021
ms.author: mesrivas
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39eba039a8f7b381e998be3d8bfe5cd213217ab3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013425"
---
# <a name="azure-synapse-access-control"></a>Azure Synapse 액세스 제어 

이 문서에서는 Azure Synapse 컴퓨팅 리소스 및 데이터에 대한 액세스를 제어하는 데 사용할 수 있는 메커니즘의 개요를 제공합니다.  

## <a name="overview"></a>개요

Azure Synapse는 다음을 통합하는 포괄적이고 세분화된 액세스 제어 시스템을 제공합니다. 
- **Azure 역할** - 리소스 관리 및 스토리지의 데이터 액세스에 사용 
- **Synapse 역할** - 코드 및 실행에 대한 라이브 액세스 관리 
- **SQL 역할** - SQL 풀의 데이터에 대한 데이터 평면 액세스 
- **Git 권한** - 연속 통합 및 배포 지원을 포함한 원본 코드 제어  

Azure Synapse 역할은 여러 범위에서 적용할 수 있는 권한 세트를 제공합니다. 이처럼 세밀한 적용이 가능하므로 관리자, 개발자, 보안 담당자 및 운영자에게 리소스와 데이터를 컴퓨팅하는 데 꼭 필요한 액세스 권한을 간편하게 부여할 수 있습니다.

사용자의 작업 역할에 맞는 보안 그룹을 사용하면 액세스 제어를 단순화할 수 있습니다. 적절한 보안 그룹의 사용자를 추가하고 제거하는 간단한 방법으로 액세스를 관리할 수 있습니다.

## <a name="access-control-elements"></a>액세스 제어 요소

### <a name="create-and-manage-azure-synapse-compute-resources"></a>Azure Synapse 컴퓨팅 리소스 만들기 및 관리

Azure 역할은 다음을 관리하는 데 사용됩니다. 
- 전용 SQL 풀
- Data Explorer 풀
- Apache Spark 풀
- 통합 런타임

이러한 리소스를 *생성* 하려면 리소스 그룹의 Azure 소유자 또는 기여자여야 합니다. 이러한 항목을 만든 후 *관리* 하려면 리소스 그룹 또는 개별 리소스에 대한 Azure 소유자 또는 기여자여야 합니다. 

### <a name="develop-and-execute-code-in-azure-synapse"></a>Azure Synapse에서 코드 개발 및 실행 

Synapse는 다음과 같은 두 가지 개발 모델을 지원합니다.

- **Synapse 라이브 개발**. Synapse Studio에서 코드를 개발하고 디버그한 다음, 코드를 **게시** 하여 저장하고 실행할 수 있습니다.  Synapse 서비스는 코드를 편집하고 실행하기 위한 믿을 수 있는 원본입니다.  게시되지 않은 작업은 Synapse Studio를 닫을 때 손실됩니다.  
- **Git 지원 개발**. Synapse Studio에서 코드를 개발하고 디버그한 다음, Git 리포지토리의 작업 분기에 변경 내용을 **커밋** 합니다. 하나 이상의 분기에서 수행한 작업은 협업 분기에 통합되며, 협업 분기에서 작업을 서비스에 **게시** 합니다. Git 리포지토리는 코드 편집을 위한 믿을 수 있는 원본이고, 서비스는 실행을 위한 믿을 수 있는 원본입니다. Synapse Studio를 닫기 전에 변경 내용을 Git 리포지토리에 커밋하거나 서비스에 게시해야 합니다. Git에서 Synapse Analytics를 사용하는 방법에 대해 [자세히 알아보세요](../cicd/continuous-integration-delivery.md).

두 개발 모델에서 Synapse Studio에 액세스할 수 있는 모든 사용자는 코드 아티팩트를 만들 수 있습니다. 그러나 아티팩트를 서비스에 게시하고, 게시된 아티팩트를 읽고, 변경 내용을 Git에 커밋하고, 코드를 실행하고, 자격 증명으로 보호된 연결된 데이터에 액세스하려면 추가 권한이 필요합니다.

### <a name="azure-synapse-roles"></a>Azure Synapse 역할

Azure Synapse 역할은 다음과 같은 작업을 허용하는 Synapse 서비스에 대한 액세스를 제어하는 데 사용됩니다. 
- 게시된 코드 아티팩트 나열 
- 코드 아티팩트, 연결된 서비스 및 자격 증명 정의 게시
- Synapse 컴퓨팅 리소스를 사용하는 코드 또는 파이프라인 실행
- 자격 증명으로 보호된 연결된 데이터에 액세스하는 코드 또는 파이프라인 실행
- 게시된 코드 아티팩트와 연결된 출력 보기
- 컴퓨팅 리소스 상태를 모니터링하고 런타임 로그 보기

Azure Synapse 역할은 작업 영역 범위에서 할당할 수도 있고 세부적인 범위에서 할당하여 특정 Azure Synapse 리소스에 부여된 권한을 제한할 수도 있습니다.

### <a name="git-permissions"></a>Git 권한

Git 모드에서 Git 지원 개발을 사용하는 경우 연결된 서비스 및 자격 증명 정의를 포함한 코드 아티팩트를 읽으려면 Synapse User 또는 Synapse RBAC(역할 기반 액세스 제어) 역할 외에 Git 권한이 필요합니다. Git 모드에서 코드 아티팩트에 대한 변경 내용을 커밋하려면 Git 권한, 작업 영역에 대한 Azure 기여자(Azure RBAC) 역할 및 Synapse Artifact Publisher(Synapse RBAC) 역할이 필요합니다.

   
### <a name="access-data-in-sql"></a>SQL의 데이터 액세스

전용 서버리스 SQL 풀을 사용하는 경우 SQL 권한을 사용하여 데이터 평면 액세스를 제어합니다. 

작업 영역 작성자는 작업 영역의 Active Directory 관리자로 할당됩니다. 만든 후에는 Azure Portal에서 이 역할을 다른 사용자 또는 보안 그룹에 할당할 수 있습니다.

**서버리스 SQL 풀**: Synapse 관리자에게는 서버리스 SQL 풀 '기본 제공'에 대한 `db_owner`(`DBO`) 권한이 부여됩니다. 다른 사용자에게 서버리스 SQL 풀에 대한 액세스 권한을 부여하려면 Synapse 관리자는 각 서버리스 풀에 대해 SQL 스크립트를 실행해야 합니다.  

**전용 SQL 풀**: Synapse 관리자는 전용 SQL 풀의 데이터에 대한 전체 액세스 권한이 있으며 다른 사용자에게 액세스 권한을 부여할 수 있습니다. Synapse 관리자는 데이터베이스 삭제를 제외하고 전용 풀에서 구성 및 유지 관리 작업을 수행할 수도 있습니다. 작업 영역 작성자와 작업 영역 MSI에게는 Active Directory 관리자 권한이 부여됩니다.  그 외의 사용자에게는 전용 SQL 풀에 대한 액세스 권한이 자동으로 부여되지 않습니다. 다른 사용자 또는 그룹에 전용 SQL 풀에 대한 액세스 권한을 부여하려면 Active Directory 관리자 또는 Synapse 관리자는 각 전용 SQL 풀에 대해 SQL 스크립트를 실행해야 합니다.

SQL 풀에서 SQL 권한을 부여하는 SQL 스크립트 예제는 [Synapse 액세스 제어를 설정하는 방법](./how-to-set-up-access-control.md)을 참조하세요.  

### <a name="accessing-data-in-data-explorer-pools"></a>Data Explorer 풀의 데이터 액세스

Data Explorer 풀로 작업할 때 데이터 평면 액세스는 Data Explorer 권한을 통해 제어됩니다. Synapse 관리자에게는 Data Explorer 풀에 대한 `All Database admin` 권한이 부여됩니다. 다른 사용자 또는 그룹에 Data Explorer 풀에 대한 액세스 권한을 부여하려면 Synapse 관리자는 [보안 역할 관리](/azure/data-explorer/kusto/management/security-roles?context=/azure/synapse-analytics/context/context)를 참조해야 합니다. 데이터 평면 액세스에 대한 자세한 내용은 [Data Explorer 액세스 제어 개요](/azure/data-explorer/kusto/management/access-control/index?context=/azure/synapse-analytics/context/context)를 참조하세요.


 ### <a name="accessing-system-managed-data-in-storage"></a>스토리지의 시스템 관리형 데이터에 액세스

서버리스 SQL 풀 및 Apache Spark 테이블은 작업 영역과 연결된 ADLS Gen2 컨테이너에 데이터를 저장합니다. 사용자가 설치한 Apache Spark 라이브러리도 동일한 스토리지 계정에서 관리됩니다. 이러한 사용 사례를 사용하도록 설정하려면 사용자 및 작업 영역 MSI에 이 작업 영역 ADLS Gen2 스토리지 컨테이너에 대한 **Storage Blob 데이터 기여자** 액세스 권한이 부여되어야 합니다.  

## <a name="using-security-groups-as-a-best-practice"></a>모범 사례로 보안 그룹 사용

개인과 그룹에게 역할을 할당하여 액세스 제어 관리를 간소화할 수 있습니다. 보안 그룹을 만들어서 Synapse 리소스 또는 아티팩트에 액세스해야 하는 조직의 가상 사용자 또는 직무를 미러링할 수 있습니다.  그 후 이러한 가상 사용자 기반 보안 그룹에 하나 이상의 Azure 역할, Synapse 역할, SQL 권한 또는 Git 권한을 할당할 수 있습니다. 보안 그룹을 잘 선택하면 사용자를 적절한 보안 그룹에 추가하여 사용자에게 필요한 권한을 쉽게 할당할 수 있습니다. 

>[!Note]
>보안 그룹을 사용하여 액세스를 관리하는 경우 변경 내용이 적용될 때까지 Azure Active Directory에서 추가 대기 시간이 발생합니다. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Synapse Studio에서 액세스 제어 적용

Synapse Studio는 권한과 현재 모드에 따라 다르게 작동합니다.
- **Synapse 라이브 모드:** 사용자에게 필요한 권한이 없는 경우 Synapse Studio는 사용자가 게시된 콘텐츠를 보거나, 콘텐츠를 게시하거나, 다른 작업을 수행하지 못하게 차단합니다.  경우에 따라 사용 또는 저장이 불가능한 코드 아티팩트를 만들 수 없습니다. 
- **Git 모드:** 현재 분기에 변경 내용을 커밋할 수 있는 Git 권한이 있는 경우 라이브 서비스(Synapse Artifact Publisher 역할) 및 작업 영역에서 Azure 기여자 역할에 대한 변경 내용을 게시할 수 있는 권한이 있는 경우 커밋 작업이 허용됩니다.  

경우에 따라 게시하거나 커밋할 권한이 없어도 코드 아티팩트를 만들 수 있습니다. 이를 통해 필요한 실행 권한으로 코드를 실행할 수 있습니다. 일반 작업에 필요한 역할에 대해 [자세히 알아보세요](./synapse-workspace-understand-what-role-you-need.md). 

Synapse Studio에서 기능을 사용하지 않도록 설정하면 도구 설명에 필수 권한이 표시됩니다. [Synapse RBAC 역할 가이드](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them)를 사용하여 누락된 권한을 제공하는 데 필요한 역할을 알아보세요.


## <a name="next-steps"></a>다음 단계

- [Synapse RBAC](./synapse-workspace-synapse-rbac.md)에 대한 자세한 정보
- [Synapse RBAC 역할](./synapse-workspace-synapse-rbac-roles.md)에 대한 자세한 정보
- 보안 그룹을 사용하여 Synapse 작업 영역에 대한 [액세스 제어를 설정하는 방법](./how-to-set-up-access-control.md) 알아보기
- [Synapse RBAC 역할 할당을 검토하는 방법](./how-to-review-synapse-rbac-role-assignments.md) 알아보기
- [Synapse RBAC 역할 할당을 관리하는 방법](./how-to-manage-synapse-rbac-role-assignments.md) 알아보기
