---
title: 서비스 엔드포인트 정책 구성
description: 무단 Azure Storage 계정으로의 반출로부터 Azure SQL Managed Instance 보호하도록 Azure Storage 서비스 엔드포인트 정책을 구성합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 9760285da827e884c5dfa49ca1da58b3e76292de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053241"
---
# <a name="configure-service-endpoint-policies-preview-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance 대한 서비스 엔드포인트 정책(미리 보기) 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

VNet(Virtual Network) Azure Storage [서비스 엔드포인트 정책을](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) 사용하면 송신 가상 네트워크 트래픽을 Azure Storage 필터링하여 특정 스토리지 계정으로의 데이터 전송을 제한할 수 있습니다.

엔드포인트 정책을 구성하고 SQL Managed Instance 연결하는 기능은 현재 미리 보기로 제공됩니다. 

## <a name="key-benefits"></a>주요 이점

Azure SQL Managed Instance 대한 가상 네트워크 Azure Storage 서비스 엔드포인트 정책을 구성하면 다음과 같은 이점이 있습니다.

- __Azure Storage Azure SQL Managed Instance 트래픽에 대한 보안 향상:__ 엔드포인트 정책은 중요 비즈니스용 데이터의 잘못되거나 악의적인 반출을 방지하는 보안 제어를 설정합니다. 트래픽은 데이터 거버넌스 요구 사항을 준수하는 스토리지 계정으로만 제한될 수 있습니다.

- __액세스할 수 있는 스토리지 계정에 대한 세부적인 제어:__ 서비스 엔드포인트 정책은 구독, 리소스 그룹 및 개별 스토리지 계정 수준에서 스토리지 계정에 대한 트래픽을 허용할 수 있습니다. 관리자는 서비스 엔드포인트 정책을 사용하여 Azure에서 조직의 데이터 보안 아키텍처를 준수할 수 있습니다.

- __시스템 트래픽은 영향을 받지 않습니다.__ 서비스 엔드포인트 정책은 Azure SQL Managed Instance 작동하는 데 필요한 스토리지에 대한 액세스를 방해하지 않습니다. 여기에는 백업, 데이터 파일, 트랜잭션 로그 파일 및 기타 자산의 스토리지가 포함됩니다.

> [!IMPORTANT]
> 서비스 엔드포인트 정책은 SQL Managed Instance 서브넷에서 시작되고 Azure Storage에서 종료되는 트래픽만 제어합니다. 예를 들어 온-프레임 BACPAC 파일로 데이터베이스 내보내기, 통합 Azure Data Factory, Azure Diagnostic 설정 통한 진단 정보 수집 또는 Azure Storage 직접 대상으로 지정하지 않는 기타 데이터 추출 메커니즘에는 정책이 영향을 미치지 않습니다.

## <a name="limitations"></a>제한 사항

Azure SQL Managed Instance 서비스 엔드포인트 정책을 사용하도록 설정하면 다음과 같은 제한 사항도 있습니다.

- 미리 보기로 제공되는 동안 이 기능은 중국 동부 2, 중국 북부 2, **미국 중부 EUAP, 미국 동부 2 EUAP,** **US Gov 애리조나,** **US Gov 텍사스,** **US Gov 버지니아** 및 **미국 중서부를** 제외하고 **SQL Managed Instance** 지원되는 모든 Azure 지역에서 사용할 수 있습니다.
- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 이 기능은 Azure Storage 대한 [서비스 엔드포인트가](../../virtual-network/virtual-network-service-endpoints-overview.md) 설정된 서브넷에서만 사용할 수 있습니다.
- Azure Storage 서비스 엔드포인트를 사용하도록 설정하면 RA-GRS(Read-Access Geo-Redundant 스토리지) 및 GRS(Geo-Redundant 스토리지) 트래픽을 지원하기 위해 가상 네트워크를 배포하는 쌍을 이루는 지역이 포함됩니다.
- 서비스 엔드포인트에 서비스 엔드포인트 정책을 할당하면 엔드포인트가 지역에서 전역 범위로 업그레이드됩니다. 즉, Azure Storage 대한 모든 트래픽은 스토리지 계정이 있는 지역에 관계없이 서비스 엔드포인트를 통과합니다.

## <a name="prepare-storage-inventory"></a>스토리지 인벤토리 준비

서브넷에서 서비스 엔드포인트 정책 구성을 시작하기 전에 관리되는 인스턴스가 해당 서브넷에서 액세스할 수 있어야 하는 스토리지 계정 목록을 작성합니다.

다음은 Azure Storage 문의할 수 있는 워크플로 목록입니다.

- Azure Storage [감사](auditing-configure.md)
- 복사 [전용 백업을](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 수행하여 Azure Storage.
- Azure Storage 데이터베이스 [복원](restore-sample-database-quickstart.md)
- BULK INSERT 또는 [OPENROWSET(BULK ...)를](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server)통해 데이터를 가져옵니다.
- Azure Storage 이벤트 파일 대상에 [확장 이벤트를](../database/xevent-db-diff-from-svr.md) 로깅합니다.
- [Azure DMS를](../../dms/tutorial-sql-server-to-managed-instance.md) Azure SQL Managed Instance 오프라인으로 마이그레이션합니다.
- [Log Replay Service를](log-replay-service-migrate.md) Azure SQL Managed Instance 마이그레이션합니다.
- 트랜잭션 복제 를 사용하여 테이블을 [동기화합니다.](replication-transactional-overview.md)

이러한 스토리지에 참여하는 모든 스토리지 계정 또는 스토리지에 액세스하는 다른 워크플로의 계정 이름, 리소스 그룹 및 구독을 적어 둡니다. 


## <a name="configure-policies"></a>정책 구성

먼저 서비스 엔드포인트 정책을 만든 다음, 정책을 SQL Managed Instance 서브넷과 연결해야 합니다. 비즈니스 요구 사항에 맞게 이 섹션의 워크플로를 수정합니다.


> [!NOTE]
> - SQL Managed Instance 서브넷에는 /Services/Azure/ManagedInstance 서비스 별칭을 포함하는 정책이 필요합니다(5단계 참조). 
> - 서비스 엔드포인트 정책이 이미 포함된 서브넷에 배포된 관리되는 인스턴스는 /Services/Azure/ManagedInstance 서비스 별칭으로 자동으로 업그레이드됩니다.

### <a name="create-a-service-endpoint-policy"></a>서비스 엔드포인트 정책 만들기

서비스 엔드포인트 정책을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. **+ 리소스 만들기** 를 선택합니다. 
1. 검색 창에서 서비스 _엔드포인트 정책을_ 입력하고 **서비스 엔드포인트 정책** 를 선택한 **다음, 만들기를** 선택합니다.

   ![서비스 엔드포인트 정책 만들기](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

1. 기본 사항 페이지에서 다음 값을 **입력합니다.**

   - 구독: 드롭다운에서 정책에 대한 구독을 선택합니다.
   - 리소스 그룹: 관리되는 인스턴스가 있는 리소스 그룹을 선택하거나 **새로 만들기를** 선택하고 새 리소스 그룹의 이름을 입력합니다.
   - 이름: 정책의 이름(예: **mySEP)을** 제공합니다.
   - 위치: 관리되는 인스턴스를 호스팅하는 가상 네트워크의 지역을 선택합니다. 

   ![서비스 엔드포인트 정책 기본 만들기](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

1. **정책 정의에서** **별칭 추가를** 선택하고 별칭 추가 창에 다음 정보를 **입력합니다.**
   - 서비스 별칭: /Services/Azure/ManagedInstance를 선택합니다.
   - **추가를** 선택하여 서비스 별칭 추가를 완료합니다.

   ![서비스 엔드포인트 정책에 별칭 추가](./media/service-endpoint-policies-configure/add-an-alias.png)

1. 정책 정의에서 리소스 아래에서 **+ 추가를** 선택하고 **리소스** **추가** 창에서 다음 정보를 입력하거나 선택합니다.
   - 서비스: **Microsoft.Storage** 선택합니다.
   - 범위: **구독의 모든 계정을** 선택합니다.
   - 구독: 허용할 스토리지 계정이 포함된 구독을 선택합니다. 이전에 만든 [Azure Storage 계정의 인벤토리를](#prepare-storage-inventory) 참조하세요.
   - **추가를** 선택하여 리소스 추가를 완료합니다.
   - 이 단계를 반복하여 구독을 추가합니다. 

   ![서비스 엔드포인트 정책에 리소스 추가](./media/service-endpoint-policies-configure/add-a-resource.png)

1. 선택 사항: 태그 아래에 있는 서비스 엔드포인트 정책에서 태그를 구성할 수 **있습니다.**
1.  **검토 + 만들기** 를 선택합니다. 정보의 유효성을 검사하고 **만들기를** 선택합니다. 추가로 편집하려면 **이전** 을 선택합니다.

   > [!TIP]
   > 먼저 전체 구독에 대한 액세스를 허용하는 정책을 구성합니다. 모든 워크플로가 정상적으로 작동하는지 확인하여 구성의 유효성을 검사합니다. 그런 다음 필요에 따라 리소스 그룹의 개별 스토리지 계정 또는 계정을 허용하도록 정책을 다시 구성합니다. 이렇게 하려면 _대신 범위:_ 필드에서 리소스 그룹의 **단일 계정** 또는 **모든 계정을** 선택하고 그에 따라 다른 필드를 채웁니다.

### <a name="associate-policy-with-subnet"></a>서브넷에 정책 연결

서비스 엔드포인트 정책을 만든 후 정책을 SQL Managed Instance 서브넷과 연결합니다.

정책을 연결하려면 다음 단계를 수행합니다.

1. Azure Portal _모든 서비스_ 상자에서 _가상 네트워크를_ 검색합니다. **가상 네트워크** 를 선택합니다.
1. 관리되는 인스턴스를 호스팅하는 가상 네트워크를 찾아 선택합니다.
1. **서브넷을** 선택하고 관리되는 인스턴스 전용 서브넷을 선택합니다. 서브넷 창에 다음 정보를 입력합니다.
    - 서비스: **Microsoft.Storage** 선택합니다. 이 필드가 비어 있으면 이 서브넷에서 Azure Storage 서비스 엔드포인트를 구성해야 합니다.
    - 서비스 엔드포인트 정책: SQL Managed Instance 서브넷에 적용하려는 서비스 엔드포인트 정책을 선택합니다.

   ![서비스 엔드포인트 정책을 서브넷에 연결](./media/service-endpoint-policies-configure/associate-service-endpoint-policy.png)

1. **저장을** 선택하여 가상 네트워크 구성을 완료합니다.

> [!WARNING]
> 이 서브넷의 정책에 별칭이 없으면 `/Services/Azure/ManagedInstance` 다음 오류가 표시 될 수 있습니다. ` Failed to save subnet 'subnet'. Error: 'Found conflicts with NetworkIntentPolicy.`
> `Details: Service endpoint policies on subnet are missing definitions`
> 이 문제를 해결하려면 별칭을 포함하도록 서브넷의 모든 정책을 `/Services/Azure/ManagedInstance` 업데이트합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 보안에](../../storage/common/storage-network-security.md)대해 자세히 알아보세요.
- SQL Managed Instance 보안 기능에 대해 [읽어보십시오.](../database/security-overview.md)
- SQL Managed Instance [연결 아키텍처를](connectivity-architecture-overview.md) 탐색합니다.
