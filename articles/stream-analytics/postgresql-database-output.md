---
title: Azure Stream Analytics에서 Azure Database for PostgreSQL 출력 (미리 보기)
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력 Azure Database for PostgreSQL 설명 합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: 6a46d1089512410f49436610996ee33f24fb8f61
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871794"
---
# <a name="azure-database-for-postgresql-output-from-azure-stream-analytics-preview"></a>Azure Stream Analytics에서 Azure Database for PostgreSQL 출력 (미리 보기)

본질적으로 관계형 인 데이터 또는 관계형 데이터베이스에서 호스트 되는 콘텐츠에 종속 된 응용 프로그램에 대 한 출력으로 [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/) 를 사용할 수 있습니다. Azure Stream Analytics 작업은 PostgreSQL 데이터베이스의 기존 테이블에 기록 합니다. 테이블 스키마는 작업 출력의 필드 및 해당 형식과 정확히 일치해야 합니다. 

PostgreSQL community edition에서 제공 하는 Azure Database for PostgreSQL는 세 가지 배포 옵션으로 제공 됩니다.
*   단일 서버
*   유연한 서버(미리 보기)
*   하이퍼스케일(Citus)

Azure Database for PostgreSQL에 대 한 자세한 내용은 [Azure Database for PostgreSQL 설명서](https://docs.microsoft.com/azure/postgresql/overview/) 를 참조 하세요.

Azure Portal를 사용 하 여 Azure Database for PostgreSQL 서버를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요. 
*   [Azure Database for PostgreSQL에 대 한 빠른 시작 – 단일 서버](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal/)
*   [Azure Database for PostgreSQL-유연한 서버에 대 한 빠른 시작](https://docs.microsoft.com/azure/postgresql/flexible-server/quickstart-create-server-portal/)
*   [Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 빠른 시작](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal/)


> [!NOTE] 
> Azure Stream Analytics Azure Database for PostgreSQL 출력에 대 한 관리 id는 현재 지원 되지 않습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에서는 Azure Database for PostgreSQL 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
|  구독 |  원하는 Azure 구독을 선택합니다. |
| 서버 또는 서버 그룹 이름 | PostgreSQL 서버용 Azure Database를 식별하는 고유한 이름입니다. 도메인 이름 postgres.database.azure.com은 사용자가 제공한 서버 이름에 추가 됩니다. 서버는 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자 이상이어야 합니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름 |
| 사용자 이름 | 데이터베이스에 대한 쓰기 액세스 권한이 있는 사용자 이름입니다. Stream Analytics은 사용자 이름/암호 인증만 지원 합니다. 사용자 이름은 username@hostname 단일 서버에 대 한 "" 형식, 유연한 서버 및 Citus (Hyperscale) 서버 그룹에 대 한 "username" 이어야 합니다. |
| 암호 | 데이터베이스에 연결하는 암호입니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분합니다. 이 테이블의 스키마는 작업 출력에서 생성하는 필드 수 및 해당 형식과 정확히 일치해야 합니다. |


## <a name="partitioning"></a>분할

파티션은 사용하도록 설정해야 하며 쿼리의 PARTITION BY 절을 기반으로 합니다. 분할 상속 옵션을 사용하도록 설정하는 경우 [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. 


## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)