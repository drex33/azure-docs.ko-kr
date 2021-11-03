---
title: Synapse 작업 영역에 대한 액세스 제어를 설정하는 방법
description: 이 문서에서는 Azure 역할, Synapse 역할, SQL 권한, Git 권한을 사용하여 Azure Synapse 작업 영역에 대한 액세스를 제어하는 방법을 설명합니다.
services: synapse-analytics
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 8/05/2021
ms.author: ronytho
ms.reviewer: jrasnick, wiassaf
ms.custom: subject-rbac-steps
ms.openlocfilehash: 3e90ab30e8eb916ef70248af32b7b95ff0a48428
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003545"
---
# <a name="how-to-set-up-access-control-for-your-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 대한 액세스 제어를 설정하는 방법 

이 문서에서는 Azure 역할, Azure Synapse 역할, SQL 권한, Git 권한을 사용하여 Microsoft Azure Synapse 작업 영역에 대한 액세스를 제어하는 방법을 설명합니다.   

이 가이드에서는 작업 영역을 설정하고 많은 Azure Synapse 프로젝트에 적합한 기본 액세스 제어 시스템을 구성합니다.  그런 다음 필요한 세부적인 제어를 위한 고급 옵션을 설명합니다.  

조직의 역할과 가상 사용자에 맞는 보안 그룹을 사용하여 Azure Synapse 액세스 제어를 간소화할 수 있습니다.  보안 그룹의 사용자를 추가하고 제거하는 것만으로 액세스를 관리할 수 있습니다.

이 연습을 시작하기 전에 [Azure Synapse 액세스 제어 개요](./synapse-workspace-access-control-overview.md)를 읽고 Azure Synapse Analytics에서 사용하는 액세스 제어 메커니즘을 숙지합니다.   

## <a name="access-control-mechanisms"></a>액세스 제어 메커니즘

> [!NOTE]
> 이 가이드에서는 여러 보안 그룹을 생성한 다음 이러한 그룹에 역할을 할당하는 방법을 사용합니다. 그룹을 설정한 후에는 작업 영역에 대한 액세스를 제어하기 위해 보안 그룹 내의 멤버 자격만 관리하면 됩니다.

Azure Synapse 작업 영역을 보호하려면 다음 항목의 구성 패턴을 따릅니다.

- **보안 그룹**: 액세스 요구 사항이 비슷한 사용자를 그룹화합니다.
- **Azure 역할**: SQL 풀, Apache Spark 풀 및 통합 런타임 생성 및 관리, ADLS Gen2 스토리지에 액세스할 수 있는 사용자를 제어합니다.
- **Synapse 역할**: 게시된 코드 아티팩트에 대한 액세스를 제어하고, Apache Spark 컴퓨팅 리소스 및 통합 런타임을 사용합니다. 
- **SQL 사용 권한**: SQL 풀에 대한 관리 및 데이터 평면 액세스를 제어합니다. 
- **Git 사용 권한**: 작업 영역에 Git 지원을 구성하는 경우 소스 제어에서 코드 아티팩트에 액세스할 수 있는 사용자를 제어합니다. 
 
## <a name="steps-to-secure-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역을 보호하는 단계

이 문서에서는 지침을 단순화하기 위해 표준 이름을 사용합니다. 표준 이름을 원하는 이름으로 바꿉니다.

|설정 | 표준 이름 | Description |
| :------ | :-------------- | :---------- |
| **Synapse 작업 영역** | `workspace1` |  Azure Synapse 작업 영역의 이름입니다. |
| **ADLSGEN2 계정** | `storage1` | 작업 영역에 사용할 ADLS 계정입니다. |
| **컨테이너** | `container1` | 작업 영역에서 기본적으로 사용하게 될 STG1의 컨테이너입니다. |
| **Active Directory 테넌트** | `contoso` | Active Directory 테넌트 이름입니다.|
||||

## <a name="step-1-set-up-security-groups"></a>1단계: 보안 그룹 설정

>[!Note] 
>미리 보기 중에는 Azure Synapse의 **Synapse SQL 관리자** 및 **Synapse Apache Spark 관리자** 역할에 매핑된 보안 그룹을 만들도록 권장되었습니다.  세분화된 새로운 Synapse RBAC 역할 및 범위가 도입됨에 따라 이제 이러한 새로운 기능을 사용하여 작업 영역에 대한 액세스를 제어하는 것이 좋습니다.  이러한 새로운 역할과 범위는 보다 유연한 구성을 제공하며 개발자는 분석 애플리케이션을 생성하기 위해 SQL과 Spark를 자주 사용하며 전체 작업 영역이 아닌 특정 리소스에 대한 액세스 권한을 부여해야 할 수도 있습니다. 시냅스 RBAC에 대해 [자세히 알아봅니다](./synapse-workspace-synapse-rbac.md).

작업 영역에 대해 다음과 같은 보안 그룹을 만듭니다.

- **`workspace1_SynapseAdministrators`** : 작업 영역을 완벽하게 제어해야 하는 사용자를 위한 그룹입니다.  처음에는 이 보안 그룹에 자신을 추가합니다.
- **`workspace1_SynapseContributors`** : 서비스에 코드를 개발, 디버그 및 게시해야 하는 개발자를 위한 그룹입니다.   
- **`workspace1_SynapseComputeOperators`** : Apache Spark 풀 및 통합 런타임을 관리 및 모니터링해야 하는 사용자를 위한 그룹입니다.
- **`workspace1_SynapseCredentialUsers`** : 작업 영역 MSI(관리되는 서비스 ID) 자격 증명을 사용하는 오케스트레이션 파이프라인을 디버그 및 실행하고 파이프라인 실행을 취소해야 하는 사용자를 위한 그룹입니다.   

작업 영역 범위에서 잠시 후에 해당 그룹에 Synapse 역할을 할당합니다.  

다음과 같이 이 보안 그룹도 만듭니다. 
- **`workspace1_SQLAdmins`** : 작업 영역의 SQL 풀에서 SQL Active Directory 관리 권한이 필요한 사용자를 위한 그룹입니다. 

`workspace1_SQLAdmins` 그룹은 만들 때 SQL 풀에 SQL 사용 권한을 구성하는 경우에 사용됩니다. 

기본 설정의 경우에는 이러한 다섯 가지 그룹으로 충분합니다. 나중에 보안 그룹을 추가하여 보다 전문화된 액세스가 필요한 사용자를 처리하거나 사용자에게 특정 리소스에 대한 액세스 권한만 부여할 수 있습니다.

> [!NOTE]
>- [이 문서](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)에서 보안 그룹을 만드는 방법을 알아보세요.
>- [이 문서](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)에서 다른 보안 그룹에서 보안 그룹을 추가하는 방법을 알아보세요.

>[!Tip]
>개별 Synapse 사용자는 Azure Portal의 Azure Active Directory를 사용하여 그룹 멤버 자격을 보고 부여된 역할을 확인할 수 있습니다.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>2단계: ADLS Gen2 스토리지 계정 준비

Azure Synapse 작업 영역은 기본 스토리지 컨테이너를 다음 용도로 사용합니다.
  - Spark 테이블의 지원 데이터 파일 저장
  - Spark 작업의 실행 로그
  - 설치하도록 선택한 라이브러리 관리

스토리지에 대한 다음 정보를 식별합니다.

- 작업 영역에 사용할 ADLS Gen2 계정입니다. 이 문서에서는 `storage1`이라고 하겠습니다. `storage1`은 작업 영역의 "주" 스토리지 계정으로 간주됩니다.
- Synapse 작업 영역에서 기본적으로 사용할 `workspace1` 내의 컨테이너입니다. 이 문서에서는 `container1`이라고 하겠습니다. 
 
- **액세스 제어(IAM)** 를 선택합니다.

- **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

- 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 기여자 |
    | 다음에 대한 액세스 할당 |SERVICEPRINCIPAL |
    | 멤버 |workspace1_SynapseAdmins, workspace1_SynapseContributors, workspace1_SynapseComputeOperators|

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-create-and-configure-your-azure-synapse-workspace"></a>3단계: Azure Synapse 작업 영역 만들기 및 구성

Azure Portal에서 Azure Synapse 작업 영역을 만듭니다.

- 구독 선택

- Azure **소유자** 역할을 보유하는 리소스 그룹을 선택하거나 만듭니다.

- `workspace1` 작업 영역 이름 지정

- 스토리지 계정으로 `storage1` 선택

- "파일 시스템"으로 사용할 컨테이너로 `container1` 선택.

- Synapse Studio에서 WS1 열기

- **관리** > **액세스 제어** 로 이동하여 다음과 같이 *작업 영역 범위* 에서 Synapse 역할을 보안 그룹으로 할당합니다.
  - **Synapse 관리자** 역할을 `workspace1_SynapseAdministrators`에 할당 
  - **Synapse 참여자** 역할을 `workspace1_SynapseContributors`에 할당 
  - **Synapse 컴퓨팅 연산자** 역할을 `workspace1_SynapseComputeOperators`에 할당

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>4단계: 기본 스토리지 컨테이너에 작업 영역 MSI 액세스 권한 부여

파이프라인을 실행하고 시스템 작업을 수행하려면 MSI(작업 영역 관리 서비스 ID)가 기본 ADLS Gen2 계정에 있는 `container1`에 액세스해야 합니다. 자세한 내용은 [Azure Synapse 작업 영역 관리 ID](synapse-workspace-managed-identity.md)를 참조하세요.

- Azure 포털 열기
- 스토리지 계정, `storage1`, `container1`을 차례로 찾습니다.
- **액세스 제어(IAM)** 를 선택합니다.
- **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
- 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 기여자 |
    | 다음에 대한 액세스 할당 | MANAGEDIDENTITY |
    | 멤버 | 관리 ID 이름  |

    > [!NOTE]
    > 관리 ID 이름은 작업 영역 이름이기도 합니다.

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>5단계: 작업 영역에서 Synapse 관리자에게 Azure 참가자 역할 부여 

SQL 풀, Apache Spark 풀 및 통합 런타임을 만들려면 사용자의 작업 영역에 Azure 참가자 역할이 하나 이상 있어야 합니다. 또한 참가자 역할을 통해 이러한 사용자는 일시 중지 및 크기 조정을 포함하여 리소스를 관리할 수 있습니다. Azure Portal 또는 Synapse Studio를 사용하여 SQL 풀, Apache Spark 풀 및 통합 런타임을 만드는 경우 리소스 그룹 수준에서 Azure 참가자 역할이 필요합니다. 

- Azure 포털 열기
- 작업 영역, `workspace1` 찾기
- **액세스 제어(IAM)** 를 선택합니다.
- **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.
- 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | 참가자 |
    | 다음에 대한 액세스 할당 | SERVICEPRINCIPAL |
    | 멤버 | workspace1_SynapseAdministrators  |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>6단계: SQL Active Directory 관리자 역할 할당

작업 영역 작성자는 작업 영역에 대한 SQL Active Directory 관리자로 자동 설정됩니다.  이 역할은 단일 사용자 또는 그룹에만 부여할 수 있습니다. 이 단계에서는 작업 영역의 SQL Active Directory 관리자를 `workspace1_SQLAdmins` 보안 그룹에 할당합니다.  이 역할을 할당하면 작업 영역의 모든 SQL 풀 및 데이터베이스에 대해 높은 권한의 관리자 권한이 이 그룹에 부여됩니다.   

- Azure 포털 열기
- `workspace1`로 이동
- **설정** 에서 **SQL Active Directory 관리자** 를 선택합니다.
- **관리자 설정** 을 선택하고 **`workspace1_SQLAdmins`** 을 선택합니다.

>[!Note]
>6단계는 선택 사항입니다.  `workspace1_SQLAdmins` 그룹에 권한이 낮은 역할을 부여하도록 선택할 수 있습니다. `db_owner` 또는 다른 SQL 역할을 할당하려면 각 SQL 데이터베이스에서 스크립트를 실행해야 합니다. 

## <a name="step-7-grant-access-to-sql-pools"></a>7단계: SQL 풀에 대한 액세스 권한 부여

기본적으로 Synapse 관리자 역할이 할당 된 모든 사용자는 `db_owner` 작업 영역에서 서버를 사용 하지 않는 SQL 풀에 SQL 역할을 할당 받습니다.

SQL 사용 권한을 사용 하 여 다른 사용자의 SQL 풀에 대 한 액세스를 제어 합니다.  SQL 사용 권한을 할당하려면 SQL 스크립트가 생성된 후 각 SQL 데이터베이스에서 실행되어야 합니다.  이러한 스크립트를 실행해야 하는 세 가지 사례가 있습니다.
1. 다른 사용자에게 서버리스 SQL 풀, ‘기본 제공’, 해당 데이터베이스에 대한 액세스 권한 부여
2. 전용 SQL 풀 데이터베이스에 대 한 사용자 액세스 권한 부여

예제 SQL 스크립트는 아래에 포함되어 있습니다.

전용 SQL 풀 데이터베이스에 대한 액세스 권한을 부여하기 위해 작업 영역 생성자 또는 `workspace1_SynapseAdministrators` 그룹의 모든 구성원이 스크립트를 실행할 수 있습니다.  

서버리스 SQL 풀, '기본 제공'에 대한 액세스 권한을 부여하기 위해 `workspace1_SQLAdmins` 그룹 또는 `workspace1_SynapseAdministrators` 그룹의 모든 구성원이 스크립트를 실행할 수 있습니다. 

> [!TIP]
> 작업 영역 수준에서 사용자에게 sysadmin 역할을 할당할 수 있는 [작업 영역 범위 사용 권한](#workspace-scoped-permission) 섹션을 제외한 모든 SQL 데이터베이스에 대한 사용자 액세스를 허용하려면 **각** SQL 풀에 대해 아래 단계를 실행해야 합니다.

### <a name="step-71-serverless-sql-pool-built-in"></a>7\.1단계: 서버리스 SQL 풀, 기본 제공

이 섹션에는 서버리스 SQL 풀, '기본 제공'의 특정 데이터베이스 또는 모든 데이터베이스에 대한 액세스 권한을 사용자에게 부여하는 방법을 보여주는 스크립트 예제가 있습니다.

> [!NOTE]
> 스크립트 예에서 *alias* 은 액세스 권한을 부여할 사용자 또는 그룹의 별칭으로, *domain* 은 사용 중인 회사 도메인으로 바꿉니다.

#### <a name="database-scoped-permission"></a>데이터베이스 범위 권한

사용자에게 **단일** 서버리스 SQL 데이터베이스에 대한 액세스 권한을 부여하려면 다음 예제의 단계를 수행합니다.

1. 로그인 만들기

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. 사용자 만들기

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. 지정된 역할의 구성원에 사용자 추가

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>작업 영역 범위 권한

작업 영역에서 **모든** 서버리스 SQL 풀에 대한 모든 권한을 부여하려면 다음 예제의 스크립트를 사용합니다.

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>7\.2단계: 전용 SQL 풀

**단일** 전용 SQL 풀 데이터베이스에 대한 액세스 권한을 부여하려면 Azure Synapse SQL 스크립트 편집기에서 다음 단계를 수행합니다.

1. *연결 대상* 드롭다운을 사용하여 선택한 대상 데이터베이스에서 다음 명령을 실행하여 데이터베이스에서 사용자를 만듭니다.

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. 다음과 같이 사용자에게 데이터베이스에 액세스할 수 있는 역할을 부여합니다.

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_owner* 권한을 원하지 않는 경우 *db_datareader* 및 *db_datawriter* 로 읽기/쓰기 권한을 수행할 수 있습니다.
> Spark 사용자가 Spark에서 SQL 풀로 직접 읽고 쓸 수 있는 경우 *db_owner* 권한이 필요합니다.

사용자를 생성한 후 쿼리를 실행하여 서버리스 SQL 풀이 스토리지 계정을 쿼리할 수 있는지 확인합니다.

## <a name="step-8-add-users-to-security-groups"></a>8단계: 보안 그룹에 사용자 추가

액세스 제어 시스템의 초기 구성이 완료되었습니다.

액세스를 관리하기 위해 사용자가 설정한 보안 그룹에 사용자를 추가하고 제거할 수 있습니다.  Azure Synapse 역할에 사용자를 수동으로 할당할 수 있지만, 이 경우 해당 구성이 일관적이지 않게 됩니다. 수동으로 할당하지 말고, 보안 그룹에 사용자를 추가 또는 제거하기만 하세요.

## <a name="step-9-network-security"></a>9단계: 네트워크 보안

작업 영역을 보호하는 최종 단계로, [작업 영역 방화벽](./synapse-workspace-ip-firewall.md)을 사용하여 네트워크 액세스를 보호해야 합니다.

- [관리형 가상 네트워크](./synapse-workspace-managed-vnet.md)의 사용 여부에 관계없이 공용 네트워크에서 작업 영역에 연결할 수 있습니다. 자세한 내용은 [연결 설정](connectivity-settings.md)을 참조하세요.
- 공용 네트워크에서의 액세스는 [공용 네트워크 액세스 기능](connectivity-settings.md#public-network-access)이나 [작업 영역 방화벽](./synapse-workspace-ip-firewall.md)을 사용하도록 설정하여 제어할 수 있습니다.
- 또는 [관리형 프라이빗 엔드포인트](synapse-workspace-managed-private-endpoints.md)와 [프라이빗 링크](../../azure-sql/database/private-endpoint-overview.md)를 사용하여 작업 영역에 연결할 수 있습니다. [Azure Synapse Analytics 관리형 가상 네트워크](synapse-workspace-managed-vnet.md)가 없는 Azure Synapse 작업 영역은 관리형 프라이빗 엔드포인트를 통해 연결할 수 없습니다.

## <a name="step-10-completion"></a>10단계: 완료

작업 영역이 완전히 구성되었으며 보안이 설정되었습니다.

## <a name="supporting-more-advanced-scenarios"></a>고급 시나리오 지원

이 가이드에서는 기본 액세스 제어 시스템을 설정하는 방법을 중점적으로 설명합니다. 추가 보안 그룹을 생성하고 이러한 그룹에 보다 구체적인 범위에서 보다 세분화된 역할을 할당하여 고급 시나리오를 지원할 수 있습니다. 다음과 같은 사례를 고려해 보세요.

CI/CD를 비롯한 고급 개발 시나리오를 위해 작업 영역에 대한 **Git 지원 사용**.  Git 모드에서 Git 권한 및 Synapse RBAC는 사용자가 작업 분기에 대 한 변경 내용을 커밋할 수 있는지 여부를 결정 합니다.  서비스에 게시하는 작업은 협업 분기에서만 수행됩니다.  작업 분기에서 업데이트를 개발하고 디버그해야 하지만 라이브 서비스에 변경 내용을 게시할 필요가 없는 개발자를 위한 보안 그룹을 만드는 것을 고려해 보세요.

특정 리소스에 대한 **개발자 액세스를 제한** 합니다.  특정 리소스에만 액세스해야 하는 개발자를 위해 세분화된 추가 보안 그룹을 생성합니다.  특정 Spark 풀, 통합 런타임 또는 자격 증명으로 범위가 지정된 적절한 Azure Synapse 역할을 그룹에 할당합니다.

**연산자가 코드 아티팩트에 액세스하지 못하도록 제한** 합니다.  Synapse 계산 리소스 및 로그의 작동 상태를 모니터링해야 하지만 코드에 액세스하거나 서비스에 업데이트를 게시할 필요가 없는 운영자를 위한 보안 그룹을 생성합니다. 특정 Spark 풀 및 통합 런타임에 컴퓨팅 연산자 역할 범위를 이러한 그룹에 할당합니다.  

## <a name="next-steps"></a>다음 단계

 - [Azure Synapse RBAC 역할 할당을 관리하는 방법](./how-to-manage-synapse-rbac-role-assignments.md)을 알아봅니다.
 - [Synapse 작업 영역](../quickstart-create-workspace.md) 만들기
