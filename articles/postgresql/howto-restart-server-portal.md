---
title: 서버 다시 시작 - Azure Portal - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Portal을 사용하여 Azure Database for PostgreSQL - 단일 서버를 다시 시작하는 방법을 설명합니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: 5f4e75c4bae971b030a93f497f52a158bf0df79d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487021"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL - 단일 서버 다시 시작
이 항목에서는 Azure Database for PostgreSQL 서버를 다시 시작하는 방법을 설명합니다. 유지 관리를 위해 서버를 다시 시작해야 할 수 있지만 이 경우 서버가 해당 작업을 수행할 때 잠깐 가동이 중단됩니다.

서비스가 다른 작업 중이면 서버가 다시 시작되지 않습니다. 예를 들어, 서비스가 vCore 크기를 조정하는 것과 같이 이전에 요청된 작업을 처리할 수 있습니다.
 
> [!NOTE] 
> 다시 시작을 완료하는 데 필요한 시간은 PostgreSQL 복구 프로세스에 따라 달라집니다. 다시 시작 시간을 줄이려면 다시 시작 전에 서버에서 발생하는 작업의 양을 최소화하는 것이 좋습니다. 검사점 빈도를 늘리고자 할 수도 있습니다. 또한 `max_wal_size` 등을 비롯한 검사점 관련 매개 변수 값을 조정할 수 있습니다. 또한 서버를 다시 시작하기 `CHECKPOINT` 전에 명령을 실행하여 복구 시간을 단축하는 것이 좋습니다. 서버를 `CHECKPOINT` 다시 시작하기 전에 명령이 수행되지 않으면 복구 시간이 길어질 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [PostgreSQL용 Azure Database 서버](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>서버 다시 시작 수행

다음 단계에서는 PostgreSQL 서버를 다시 시작합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Database for PostgreSQL 서버를 선택합니다.

2. 서버 **개요** 페이지의 도구 모음에서 **다시 시작** 을 클릭합니다.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL - 개요 - 다시 시작 단추":::

3. **예** 를 클릭하여 서버 다시 시작을 확인합니다.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL - 다시 시작 확인":::

4. 서버 상태가 "다시 시작하는 중"으로 변경되는지 확인합니다.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL - 다시 시작 상태":::

5. 서버가 성공적으로 다시 시작되는지 확인합니다.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL - 다시 시작 성공":::

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL에서 매개 변수를 설정하는 방법](howto-configure-server-parameters-using-portal.md)에 대해 알아보기
