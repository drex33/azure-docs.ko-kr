---
title: 포함 파일
description: 포함 파일
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 11/16/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: a8826948dbb6fc6843f256dcdabba60ac1b770b7
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814251"
---
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.


다음 단계에 따라 Azure Database for PostgreSQL 서버를 만듭니다.
1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 클릭합니다.
2. **새로 만들기** 페이지에서 **데이터베이스** 를 선택하고, **데이터베이스** 페이지에서 **PostgreSQL용 Azure Database** 를 선택합니다.
3. 배포 옵션을 보려면 **하이퍼스케일(Citus) 서버 그룹** 아래에서 **만들기** 단추를 클릭합니다.
4. 새 서버 세부 정보 양식을 다음 정보로 작성합니다.
   - 리소스 그룹: 이 필드의 텍스트 상자 아래에 있는 **새로 만들기** 링크를 클릭합니다. **myresourcegroup** 과 같은 이름을 입력합니다.
   - 서버 그룹 이름: 새 서버 그룹의 고유한 이름을 입력합니다. 이 이름은 서버 하위 도메인에도 사용됩니다.
   - 관리자 사용자 이름: 현재 **citus** 값이 필요하며 변경할 수 없음.
   - 암호: 8자 이상이어야 하며, 영어 대문자, 영어 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등) 중 세 가지 범주의 문자를 포함해야 합니다.
   - 위치: 데이터에 가장 빠르게 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.

   > [!IMPORTANT]
   > 여기에서 지정하는 서버 관리자 암호는 서버 및 해당 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

5. **서버 그룹 구성** 을 클릭합니다.
   - **계층** 에 대한 **기본** 라디오 단추를 선택합니다.
   - **저장** 을 클릭합니다.
6. 화면 아래쪽에서 **다음: 네트워킹 >** 을 선택합니다.

7. **네트워킹** 탭에서 **Azure 내의 Azure 서비스 및 리소스에서 이 서버 그룹에 대한 퍼블릭 액세스 허용** 을 선택합니다. 그런 다음, **+ 현재 클라이언트 IP 주소 추가** 를 선택합니다.
   ![선택된 공용 엔드포인트](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
   > [!NOTE]
   > Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 5432를 열지 않으면 하이퍼스케일(Citus) 클러스터에 연결할 수 없습니다.
   >

8. **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭하여 서버를 프로비저닝합니다. 프로비전하는 데 몇 분이 걸립니다.
9. 배포를 모니터링하도록 페이지가 리디렉션됩니다. 실시간 상태가 **배포 진행 중** 에서 **배포가 완료됨** 으로 바뀌면 페이지 왼쪽에서 **출력** 메뉴 항목을 클릭합니다.
10. 출력 페이지에는 코디네이터 호스트 이름이 포함되어 있고, 그 옆에는 이 값을 클립보드에 복사하는 단추가 있습니다. 나중에 사용할 수 있도록 이 정보를 기록해 둡니다.

## <a name="connect-to-the-database-using-psql"></a>psql을 사용하여 데이터베이스에 연결

Azure Database for PostgreSQL을 만들 때 **citus** 라는 기본 데이터베이스가 만들어집니다. 데이터베이스 서버에 연결하려면 연결 문자열 및 관리자 암호가 필요합니다.

1. 연결 문자열을 가져옵니다. 서버 그룹 페이지에서 **연결 문자열** 메뉴 항목을 클릭합니다. (**설정** 아래에 있습니다.) **psql** 로 표시된 문자열을 찾습니다. 다음과 같은 형식입니다.

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   문자열을 복사합니다. “{your\_password}”를 이전에 선택한 관리 암호로 바꾸어야 합니다. 시스템은 일반 텍스트 암호를 저장하지 않으므로 문자열에 표시할 수도 없습니다.

2. 로컬 컴퓨터에서 터미널 창을 엽니다.

3. 프롬프트에서 [psql](https://www.postgresql.org/docs/current/app-psql.html) 유틸리티를 사용하여 Azure Database for PostgreSQL 서버에 연결합니다. 따옴표 안에 연결 문자열을 전달합니다. 암호가 포함되어 있는지 확인합니다.
   ```bash
   psql "host=..."
   ```

   예를 들어 다음 명령은 **mydemoserver** 서버 그룹의 코디네이터 노드에 연결합니다.

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
