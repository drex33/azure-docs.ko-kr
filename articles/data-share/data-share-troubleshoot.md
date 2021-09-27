---
title: Azure Data Share 문제 해결
description: Azure Data Share에서 데이터 공유를 만들거나 받을 때 초대 및 오류 관련 문제를 해결하는 방법에 대해 알아봅니다.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 09/10/2021
ms.openlocfilehash: da746d14d891c875c9bed49c94e8b408d956e29e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811256"
---
# <a name="troubleshoot-common-problems-in-azure-data-share"></a>Azure Data Share의 일반적인 문제 해결 

이 문서에서는 Azure Data Share의 일반적인 문제를 해결하는 방법을 설명합니다. 

## <a name="azure-data-share-invitations"></a>Azure Data Share 초대 

경우에 따라 새 사용자가 이메일 초대에서 **초대 수락** 을 선택하면 빈 초대 목록이 표시될 수 있습니다. 이 문제는 다음 원인 중 하나 때문일 수 있습니다:

* **Azure Data Share 서비스가 Azure 테넌트의 Azure 구독에 대한 리소스 공급자로 등록되어 있지 않습니다.** 이 문제는 Azure 테넌트에 데이터 공유 리소스가 없는 경우에 발생합니다. 

    Azure Data Share 리소스를 만들면 Azure 구독에 리소스 공급자가 자동으로 등록됩니다. 다음 단계를 수행하여 Data Share 서비스를 수동으로 등록할 수도 있습니다. 이 단계를 완료하려면 Azure 구독에 대한 [참가자 역할](../role-based-access-control/built-in-roles.md#contributor)이 필요합니다. 

    1. Azure Portal에서 **구독** 으로 이동합니다.
    1. Azure Data Share 리소스를 만드는 데 사용할 구독을 선택합니다.
    1. **리소스 공급자** 를 선택합니다.
    1. **Microsoft.DataShare** 를 검색합니다.
    1. **등록** 을 선택합니다.

* **초대가 Azure 로그인 이메일 주소 대신 이메일 별칭으로 전송됩니다.** Azure Data Share 서비스를 이미 등록했거나 Azure 테넌트에서 데이터 공유 리소스를 만들었지만 여전히 초대를 볼 수 없는 경우 이메일 별칭이 수신자로 목록에 추가되었을 수 있습니다. 데이터 공급자에 문의하여 이메일 별칭이 아닌 Azure 로그인 이메일 주소로 초대가 전송되는지 확인합니다.

* **초대가 이미 수락되었습니다.** 이메일의 링크를 클릭하면 Azure Portal의 **데이터 공유 초대** 페이지로 이동합니다. 이 페이지에는 보류 중인 초대 목록만 나타납니다. 수락된 초대가 페이지에 표시되지 않습니다. 받은 공유를 보고 대상 Azure Data Explorer 클러스터 설정을 구성하려면 초대를 수락하는 데 사용한 데이터 공유 리소스로 이동합니다.

* **사용자는 테 넌 트의 게스트 사용자입니다.** 테 넌 트의 게스트 사용자 인 경우 초대를 표시 하기 전에 테 넌 트의 메일 주소를 확인 해야 합니다. 확인 된 후에는 12 개월 동안 유효 합니다. 

## <a name="creating-and-receiving-shares"></a>공유 만들기 및 받기

새 공유를 만들거나 데이터 세트를 추가하거나 데이터 집합을 매핑할 때 다음 오류가 나타날 수 있습니다:

* 데이터 집합을 추가하지 못했습니다.
* 데이터 집합을 매핑하지 못했습니다.
* 데이터 공유 리소스 x 액세스 권한을 y에 부여할 수 없습니다.
* x에 대한 적절한 권한이 없습니다.
* Azure Data Share 계정에 대한 쓰기 권한을 하나 이상의 선택한 리소스에 추가할 수 없습니다.

Azure 데이터 저장소에 대한 권한이 부족한 경우 이러한 오류 중 하나가 표시될 수 있습니다. 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md)을 참조하세요. 

Azure 데이터 저장소에서 데이터를 공유하거나 수신하려면 쓰기 권한이 필요합니다. 이 사용 권한은 일반적으로 참가자 역할의 일부입니다. 

Azure 데이터 저장소에서 처음으로 데이터를 공유하거나 받는 경우에도 *Microsoft.Authorization/role assignments/write* 권한이 필요합니다. 이 권한은 일반적으로 소유자 역할의 일부입니다. Azure 데이터 저장소 리소스를 만든 경우에도 반드시 리소스 소유자일 필요는 없습니다. 

적절한 권한이 있는 경우 Azure Data Share 서비스는 데이터 공유 리소스의 관리 ID가 데이터 저장소에 액세스할 수 있도록 자동으로 허용합니다. 이 프로세스는 몇 분 정도 걸릴 수 있습니다. 이러한 지연으로 인해 오류가 발생하는 경우 몇 분 후에 다시 시도하세요.

SQL 기반 공유에는 추가 권한이 필요합니다. 사전 요구 사항에 대한 자세한 내용은 [SQL 원본에서 공유](how-to-share-from-sql.md)를 참조하세요.

## <a name="snapshots"></a>스냅샷
다양한 이유로 스냅샷이 실패할 수 있습니다. 스냅샷의 시작 시간을 선택한 다음 각 데이터 집합의 상태를 선택하여 자세한 오류 메시지를 엽니다. 

스냅샷은 일반적으로 다음과 같은 이유로 실패합니다:

* 데이터 공유에 원본 데이터 저장소에서 읽거나 대상 데이터 저장소에 쓸 수 있는 권한이 없습니다. 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md)을 참조하세요. 처음 스냅샷을 만드는 경우 데이터 공유 리소스가 Azure 데이터 저장소에 액세스하는 데 몇 분 정도 걸릴 수 있습니다. 몇 분 후에 다시 시도해 보십시오.
* 원본 데이터 저장소 또는 대상 데이터 저장소에 대한 데이터 공유 연결이 방화벽에 의해 차단되었습니다.
* 공유 데이터 집합, 원본 데이터 저장소 또는 대상 데이터 저장소가 삭제되었습니다.

스토리지 계정의 경우 스냅샷이 만들어지는 동안 원본 파일이 업데이트되기 때문에 스냅샷이 실패할 수 있습니다. 따라서 0 바이트 파일은 대상에 표시될 수 있습니다. 원본에서 업데이트한 후에는 스냅샷이 성공해야 합니다.

SQL 원본의 경우 다음과 같은 이유로 스냅샷이 실패할 수 있습니다.

* 데이터 공유 권한을 부여하는 원본 SQL 스크립트 또는 대상 SQL 스크립트가 실행되지 않았습니다. 또는 Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL Data Warehouse)의 경우 스크립트는 Azure Active Directory 인증이 아닌 SQL 인증을 사용하여 실행됩니다. 아래 쿼리를 실행하여 Data Share 계정에 SQL 데이터베이스에 대한 적절한 권한이 있는지 확인할 수 있습니다. 원본 SQL 데이터베이스의 경우 쿼리 결과는 Data Share 계정에 *db_datareader* 역할이 있음을 표시해야 합니다. 대상 SQL 데이터베이스의 경우 쿼리 결과는 Data Share 계정에 *db_datareader*, *db_datawriter* 및 *db_dlladmin* 역할이 있음을 표시해야 합니다.

    ```sql
        SELECT DP1.name AS DatabaseRoleName,
        isnull (DP2.name, 'No members') AS DatabaseUserName
        FROM sys.database_role_members AS DRM
        RIGHT OUTER JOIN sys.database_principals AS DP1
        ON DRM.role_principal_id = DP1.principal_id
        LEFT OUTER JOIN sys.database_principals AS DP2
        ON DRM.member_principal_id = DP2.principal_id
        WHERE DP1.type = 'R'
        ORDER BY DP1.name; 
     ``` 

* 원본 데이터 저장소 또는 대상 SQL 데이터 저장소가 일시 중지되었습니다.
* 스냅샷 프로세스나 대상 데이터 저장소는 SQL 데이터 형식을 지원하지 않습니다. 자세한 내용은 [SQL 원본에서 공유](how-to-share-from-sql.md#supported-data-types)를 참조하세요.
* 원본 데이터 저장소 또는 대상 SQL 데이터 저장소가 다른 프로세스에 의해 잠겨 있습니다. Azure Data Share는 이러한 데이터 저장소를 잠그지 않습니다. 그러나 이러한 데이터 저장소에 대한 기존 잠금으로 인해 스냅샷이 실패할 수 있습니다.
* 대상 SQL 테이블은 외래 키 제약 조건에 의해 참조됩니다. 스냅샷 중에 대상 테이블의 이름이 원본 데이터의 테이블과 동일한 경우 Azure Data Share는 테이블을 삭제하고 새 테이블을 만듭니다. 외래 키 제약 조건에서 대상 SQL 테이블을 참조하는 경우 테이블을 삭제할 수 없습니다.
* 대상 CSV 파일이 생성되지만 Excel에서 데이터를 읽을 수 없습니다. 원본 SQL 테이블에 영어가 아닌 문자를 포함하는 데이터가 포함된 경우 이 문제가 나타날 수 있습니다. Excel에서 **데이터 가져오기** 탭을 선택하고 CSV 파일을 선택합니다. 파일 원본 **65001: 유니코드(UTF-8)** 를 선택하고 데이터를 로드합니다.

## <a name="update-snapshot-schedule"></a>스냅샷 일정 업데이트
데이터 공급자가 전송된 공유에 대한 스냅샷 일정을 업데이트한 후에는 데이터 소비자가 이전 스냅샷 일정을 사용하지 않도록 설정한 다음, 수신된 공유에 대해 업데이트된 스냅샷 일정을 사용하도록 설정해야 합니다. 스냅샷 일정은 UTC로 저장되며, 컴퓨터 현지 시간으로 UI에 표시됩니다. 일광 절약 시간제에 맞게 자동으로 조정되지 않습니다.  

## <a name="in-place-sharing"></a>내부 공유
다음과 같은 이유로 Azure Data Explorer 클러스터에 대한 데이터 세트 매핑이 실패할 수 있습니다.

* 사용자에게 Azure Data Explorer 클러스터에 대한 *쓰기* 권한이 없습니다. 이 사용 권한은 일반적으로 참가자 역할의 일부입니다. 
* 원본 또는 대상 Azure Data Explorer 클러스터가 일시 중지됩니다.
* 원본 Azure Data Explorer 클러스터는 EngineV2이고 대상은 EngineV3이거나 그 반대입니다. 서로 다른 엔진 버전의 Azure Data Explorer 클러스터 간 공유는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요. 

데이터를 받는 방법을 알아보려면 [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서로 계속 진행하세요.