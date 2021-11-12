---
title: 삭제 된 Azure Database for MySQL 유연한 서버 복원
description: 이 문서에서는 Azure Portal를 사용 하 Azure Database for MySQL 유연한 서버에서 삭제 된 서버를 복원 하는 방법을 설명 합니다.
author: adig
ms.author: adig
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: a41aad558c067247868e1f83118c5feb86e83b37
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352783"
---
# <a name="restore-a-deleted-azure-database-for-mysql-flexible-server"></a>삭제 된 Azure Database for MySQL 유연한 서버 복원

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

유연한 서버를 삭제 하면 서비스에서 서버 백업을 최대 5 일까지 보존할 수 있습니다. 서버 백업은 서버가 원래 있던 Azure 구독 에서만 액세스 하 고 복원할 수 있습니다. 다음 권장 단계를 수행 하 여 서버 삭제 시점부터 5 일 이내에 삭제 된 MySQL 유연한 서버 리소스를 복구할 수 있습니다. 서버에 대한 백업을 계속 사용할 수 있고 시스템에서 삭제되지 않은 경우에만 권장 단계가 작동합니다.

## <a name="pre-requisites"></a>필수 구성 요소
삭제 된 Azure Database for MySQL 유연한 서버를 복원 하려면 다음이 필요 합니다.
- 원래 서버를 호스트하는 Azure 구독 이름
- 서버를 만든 위치

## <a name="steps-to-restore"></a>복원하는 단계

1. Azure portal의 모니터링 블레이드에서 [활동 로그](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)로 이동합니다. 

2. 활동 로그에서 **필터 추가** 를 표시된 대로 클릭하고 다음과 같이 필터를 설정합니다. 

    - **구독** = 삭제된 서버를 호스트하는 구독
    - **리소스 종류** = Azure Database for MySQL 유연한 서버 (flexibleServers) 
    - **작업** = MySQL 서버 삭제 (flexibleServers/delete) 
 
     [![MySQL 서버 삭제 작업에 대해 필터링된 활동 로그](./media/how-to-restore-server-portal/monitor-log-delete-server.png)](./media/how-to-restore-server-portal/monitor-log-delete-server.png#lightbox)
   
 3. MySQL Server 삭제 이벤트를 두 번 클릭하고 JSON 탭을 클릭한 다음 JSON 출력에서 "resourceId" 및 "submissionTimestamp" 특성을 확인합니다. ResourceId는 다음 형식으로 되어 있습니다./subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/flexibleServers/deletedserver.
 
 4. [서버 REST API 만들기 페이지](/rest/api/mysql/flexibleserver/servers/create)로 이동하여 녹색으로 강조 표시된 "시도" 탭을 클릭하고 Azure 계정으로 로그인합니다.
 
 5. 3 단계에서 캡처된 "resourceId" 특성에서 파생 된 resourceGroupName, serverName (삭제 된 서버 이름) 및 subscriptionId를 제공 하 고, api 버전은 이미지에 표시 된 것 처럼 미리 채워져 있습니다.
 
     [![REST API를 사용하여 서버 만들기](./media/how-to-restore-server-portal/server-create-rest-api.png)](./media/how-to-restore-server-portal/server-create-rest-api.png#lightbox)
  
 6. 요청 본문 섹션에서 아래로 스크롤하고 다음 내용을 붙여넣습니다.
 
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerResourceId": "resourceId"
            }
    }
    ```
7. 위의 요청 본문에서 다음 값을 바꿉니다.
   * "Dropped server Location"은 삭제된 서버가 원래 만들어졌던 Azure 지역으로 바꿉니다.
   * "submissionTimestamp" 및 "resourceId"는 3단계에서 캡처된 값으로 바꿉니다. 
   * "restorePointInTime"의 경우 "submissionTimestamp"에서 **15분** 을 뺀 값을 지정하여 명령이 오류를 출력하지 않도록 합니다.
   
8. 응답 코드 201 또는 202가 표시되면 복원 요청이 성공적으로 제출된 것입니다. 

9. 원본 서버에 프로비전된 컴퓨팅 리소스 및 데이터베이스 크기에 따라 서버를 만드는 데 시간이 걸릴 수 있습니다. 복원 상태는 다음에 대 한 필터링을 통해 활동 로그에서 모니터링할 수 있습니다. 
   - **구독** = 사용자 구독
   - **리소스 종류** = Azure Database for MySQL 유연한 서버 (flexibleServers) 
   - **작업** = MySQL Server 만들기 업데이트

## <a name="next-steps"></a>다음 단계
- 5일 이내에 서버를 복원하려고 시도 중인데 앞에서 설명한 단계를 정확하게 따른 후에도 여전히 오류가 발생하는 경우 도움을 받기 위해 지원 인시던트를 엽니다. 5일 후에 삭제된 서버를 복원하려고 시도 중인 경우 백업 파일을 찾을 수 없으므로 오류가 예상됩니다. 이 시나리오에서는 지원 티켓을 열지 않습니다. 백업이 시스템에서 삭제된 경우 지원 팀에서는 어떤 도움도 제공할 수 없습니다. 
- 서버를 실수로 삭제하는 것을 방지하려면 [리소스 잠금](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)을 사용하는 것이 좋습니다.
