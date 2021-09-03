---
title: 복원 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL - Azure Portal
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 복원 작업을 수행하는 방법을 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/09/2021
ms.openlocfilehash: fc52bac390763418d21cb001e5ae224acbaaa906
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113595528"
---
# <a name="point-in-time-restore-of-a-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹의 지정 시간 복원

이 문서에서는 백업을 사용하여 하이퍼스케일(Citus) 서버 그룹에서 [지정 시간 복원](concepts-hyperscale-backup.md#restore)을 수행하는 단계별 절차를 제공합니다. 가장 빠른 백업 또는 보존 기간 내의 사용자 지정 복원 지점으로 복원할 수 있습니다.

## <a name="restoring-to-the-earliest-restore-point"></a>가장 빠른 복원 지점으로 복원

하이퍼스케일(Citus) 서버 그룹을 기존의 가장 오래된 백업으로 복원하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 복원하려는 서버 그룹을 선택합니다.

2.  왼쪽 패널에서 **개요** 를 클릭하고 **복원** 을 클릭합니다.

    > [!IMPORTANT]
    > 서버 그룹에 아직 **복원** 단추가 없는 경우 서버 그룹을 복원하기 위해 Azure 지원 요청을 여세요.

3.  복원 페이지에서 **가장 빠른** 복원 지점과 **사용자 지정** 복원 지점 중 선택하라는 메시지가 나타나며 가장 빠른 날짜가 표시됩니다.

4.  **가장 빠른 복원 지점** 을 선택합니다.

5.  **새 서버로 복원** 필드에 새 서버 그룹 이름을 제공합니다. 다른 필드(구독, 리소스 그룹 및 위치)도 표시되지만 편집할 수는 없습니다.

6.  **확인** 을 클릭합니다.

7.  복원 작업이 시작되었다는 알림이 표시됩니다.

마지막으로 [복원 후 작업](#post-restore-tasks)을 수행합니다.

## <a name="restoring-to-a-custom-restore-point"></a>사용자 지정 복원 지점으로 복원

다음 단계를 수행하여 하이퍼스케일(Citus) 서버 그룹을 선택한 날짜 및 시간으로 복원합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 복원하려는 서버 그룹을 선택합니다.

2.  왼쪽 패널에서 **개요** 를 클릭하고 **복원** 을 클릭합니다.

    > [!IMPORTANT]
    > 서버 그룹에 아직 **복원** 단추가 없는 경우 서버 그룹을 복원하기 위해 Azure 지원 요청을 여세요.

3.  복원 페이지에서 **가장 빠른** 복원 지점과 **사용자 지정** 복원 지점 중 선택하라는 메시지가 나타나며 가장 빠른 날짜가 표시됩니다.

4.  **사용자 지정 복원 지점** 을 선택합니다.

5.  **복원 지점(UTC)** 에 대한 날짜 및 시간을 선택하고 **새 서버로 복원** 필드에 새 서버 그룹 이름을 제공합니다. 다른 필드(구독, 리소스 그룹 및 위치)도 표시되지만 편집할 수는 없습니다.
 
6.  **확인** 을 클릭합니다.

7.  복원 작업이 시작되었다는 알림이 표시됩니다.

마지막으로 [복원 후 작업](#post-restore-tasks)을 수행합니다.

## <a name="post-restore-tasks"></a>복원 후 작업

복원 후 다음을 수행하여 사용자 및 애플리케이션을 백업하고 실행해야 합니다.

* 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
* 사용자가 연결할 수 있도록 적절한 서버 수준 방화벽이 있는지 확인합니다. 이러한 규칙은 원본 서버 그룹에서 복사되지 않습니다.
* 필요에 따라 PostgreSQL 서버 매개 변수를 조정합니다. 매개 변수는 원본 서버 그룹에서 복사되지 않습니다.
* 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
* 필요에 따라 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

* 하이퍼스케일(Citus)의 [백업 및 복원](concepts-hyperscale-backup.md)에 대해 자세히 알아봅니다.
* 하이퍼스케일(Citus) 서버 그룹에서  [제안된 경고](./howto-hyperscale-alert-on-metric.md#suggested-alerts)를 설정합니다.
