---
title: 개인 액세스 사용 (미리 보기)-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 서버 그룹에서 개인 링크를 설정 하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: e7694cd55eb6b6da7adb0313de47575880854cc5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075850"
---
# <a name="private-access-preview-in-azure-database-for-postgresql-hyperscale-citus"></a>Citus (Azure Database for PostgreSQL Hyperscale)의 개인 액세스 (미리 보기)

[개인 액세스](concepts-hyperscale-private-access.md) (미리 보기)를 사용 하면 Azure 가상 네트워크의 리소스를 Citus (hyperscale) 서버 그룹의 노드에 안전 하 게 연결할 수 있습니다. 이 방법는 가상 네트워크 및 서브넷을 이미 만들었다고 가정 합니다. 필수 구성 요소를 설정 하는 예제는 [개인 액세스 자습서](tutorial-hyperscale-private-access.md)를 참조 하세요.

## <a name="create-a-server-group-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 서버 그룹 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다.

2. **새로 만들기** 페이지에서 **데이터베이스** 를 선택하고, **데이터베이스** 페이지에서 **PostgreSQL용 Azure Database** 를 선택합니다.

3. 배포 옵션의 경우 **Citus (Hyperscale) 서버 그룹** 에서 **만들기** 단추를 선택 합니다.

4. 리소스 그룹, 원하는 서버 그룹 이름, 위치 및 데이터베이스 사용자 암호를 사용 하 여 새 서버 세부 정보 양식을 작성 합니다.

5. **서버 그룹 구성** 을 선택 하 고 원하는 계획을 선택한 다음 **저장** 을 선택 합니다.

6. 완료되면 **다음: 네트워킹** 을 선택합니다.

7. **개인 액세스 (미리 보기)** 를 선택 합니다.

    > [!NOTE]
    >
    > 개인 액세스는 [특정 지역](concepts-hyperscale-limits.md#regions)에서만 미리 볼 수 있습니다.
    >
    > 서버 그룹이 허용 된 지역 내에 있는 경우에도 서버 그룹에 대해 개인 액세스 옵션을 선택할 수 없는 경우 Azure [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 열고 AZURE 구독 ID를 포함 하 여 액세스 권한을 얻습니다.

8. **개인 끝점 만들기** 라는 화면이 나타납니다. 기존 리소스에 대 한 적절 한 값을 선택 하 고 **확인** 을 클릭 합니다.

    - **리소스 그룹**
    - **위치**
    - **이름**
    - **대상 하위 리소스**
    - **가상 네트워크**
    - **서브넷**
    - **프라이빗 DNS 영역과 통합**

9. 개인 끝점을 만든 후 **검토 + 만들기** 를 선택 하 여 Citus (hyperscale) 서버 그룹을 만듭니다.

## <a name="enable-private-access-on-an-existing-server-group"></a>기존 서버 그룹에 대 한 개인 액세스를 사용 하도록 설정

기존 서버 그룹의 노드에 대 한 개인 끝점을 만들려면 서버 그룹에 대 한 **네트워킹** 페이지를 엽니다.

1. **+ 개인 끝점 추가** 를 선택 합니다.

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="네트워킹 화면":::

1. **기본 사항** 탭에서 **구독**, **리소스 그룹** 및 **지역을** 확인 합니다. 끝점의 **이름** (예:)을 입력 `my-server-group-eq` 합니다.

    > [!NOTE]
    >
    > 달리 선택 해야 하는 이유가 없다면 서버 그룹의 구독과 일치 하는 구독과 지역을 선택 하는 것이 좋습니다.  양식 필드의 기본값은 올바르지 않을 수 있습니다. 확인 하 고 필요한 경우 업데이트 합니다.

2. **다음: 리소스 >** 를 선택 합니다. **대상 하위 리소스** 에서 서버 그룹의 대상 노드를 선택 합니다. 일반적으로 `coordinator` 는 원하는 노드입니다.

3. **다음: 구성 >** 을 선택합니다. 원하는 **가상 네트워크** 및 **서브넷** 을 선택 합니다. **사설 DNS 통합** 을 사용자 지정 하거나 기본 설정을 적용 합니다.

4. **다음: 태그 >** 선택 하 고 원하는 태그를 추가 합니다.

5. 마지막으로 **검토 + >만들기** 를 선택 합니다. 설정을 검토 하 고 만족할 경우 **만들기** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [개인 액세스](concepts-hyperscale-private-access.md) (미리 보기)에 대해 자세히 알아보세요.
* 실행 중인 개인 액세스 (미리 보기)를 보려면 [자습서](tutorial-hyperscale-private-access.md) 를 따르세요.
