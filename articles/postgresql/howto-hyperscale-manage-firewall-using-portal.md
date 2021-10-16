---
title: 방화벽 규칙 관리 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL의 방화벽 규칙 만들기 및 관리 - Azure Portal을 사용하여 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: 23874f83e55fb8ff1ea3f60872cdfc10a64dfc80
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130071583"
---
# <a name="manage-public-access-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL 대한 공용 액세스 관리 - 하이퍼스케일(Citus)

서버 수준 방화벽 규칙을 사용하여 공용 인터넷의 지정된 IP 주소(또는 IP 주소 범위)에서 하이퍼스케일(Citus) 코디네이터 노드에 대한 공용 [액세스를](concepts-hyperscale-firewall-rules.md) 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 서버 그룹 [Azure Database for PostgreSQL – 하이퍼스케일(Citus) 서버 그룹](quickstart-create-hyperscale-portal.md)을 만듭니다.

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal에서 서버 수준 방화벽 규칙 만들기

> [!NOTE]
> 이러한 설정은 Azure Database for PostgreSQL – 하이퍼스케일(Citus) 서버 그룹을 만드는 동안에도 액세스할 수 있습니다. **네트워킹** 탭에서 **퍼블릭 엔드포인트** 를 클릭합니다.

> :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png" alt-text="Azure Portal - 네트워킹 탭":::

1. PostgreSQL 서버 그룹 페이지의 보안 머리글 아래에서 **네트워킹** 을 클릭하여 방화벽 규칙을 엽니다.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal - 네트워킹 클릭":::

2. 원하는 경우 **작업자 노드에 대한 액세스 사용을 선택합니다.** 이 옵션을 사용하면 방화벽 규칙에서 코디네이터 노드뿐만 아니라 모든 작업자 노드에 대한 액세스를 허용합니다.

3. **현재 클라이언트 IP 주소 추가** 를 클릭하여 Azure 시스템에서 인식된 컴퓨터의 공용 IP 주소로 방화벽 규칙을 만듭니다.

또는 **+ 0.0.0.0 - 255.255.255.255 추가**(옵션 B 오른쪽)를 클릭하면 IP 뿐만 아니라 전체 인터넷에서 코디네이터 노드의 포트 5432에 액세스할 수 있습니다. 이 경우 클라이언트는 클러스터를 사용하려면 여전히 올바른 사용자 이름 및 암호로 로그인해야 합니다. 그렇지만 전 세계 액세스는 짧은 기간 동안 프로덕션 이외 데이터베이스에 대해서만 허용하는 것이 좋습니다.

4. 구성을 저장하기 전에 사용자의 IP 주소를 확인합니다. 상황에 따라 Azure Portal에서 관찰하는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용된 IP 주소와 다릅니다. 따라서 규칙 기능을 예상대로 만들려면 시작 IP 및 끝 IP를 변경해야 할 수 있습니다.
   검색 엔진 또는 다른 온라인 도구를 사용하여 사용자 고유의 IP 주소를 확인합니다. 예를 들어 "내 IP는 무엇입니까"를 검색합니다.

   :::image type="content" source="./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="내 IP 주소는 무엇입니까에 대한 Bing 검색":::

5. 주소 범위를 더 추가합니다. 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 단일 IP 주소에 규칙을 제한하려는 경우 시작 IP 및 끝 IP에 대한 필드에 동일한 주소를 입력합니다. 방화벽을 열면 관리자, 사용자 및 애플리케이션이 포트 5432의 코디네이터 노드에 액세스할 수 있습니다.

6. 도구 모음에서 **저장** 을 클릭하여 이 서버 수준 방화벽 규칙을 저장합니다. 방화벽 규칙에 대한 업데이트가 성공적으로 수행되었는지 확인될 때까지 기다립니다.

## <a name="connecting-from-azure"></a>Azure에서 연결

Azure에서 호스트된 애플리케이션(예: Azure Web Apps 애플리케이션 또는 Azure VM에서 실행되는 애플리케이션)에 하이퍼스케일(Citus) 데이터베이스 액세스 권한을 부여하는 편리한 방법이 있습니다. 네트워킹 창에서 **포털에서 Azure 서비스 및 리소스가 이 서버 그룹에 액세스하도록 허용** 확인란을 선택하고 **저장을** 누릅니다. 

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure 포털을 통해 기존 서버 수준 방화벽 규칙 관리
방화벽 규칙을 관리하는 단계를 반복합니다.
* 현재 컴퓨터를 추가하려면 **현재 클라이언트 IP 주소 추가** 를 클릭합니다. **저장** 을 클릭하여 변경 내용을 저장합니다.
* 더 많은 IP 주소를 추가하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력합니다. **저장** 을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다. **저장** 을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 삭제하려면 줄임표 [...]를 클릭하고 **삭제** 를 클릭하여 규칙을 제거합니다. **저장** 을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계
- 연결 문제를 해결하는 방법을 비롯하여 [방화벽 규칙의 개념](concepts-hyperscale-firewall-rules.md)에 대해 자세히 알아보세요.
