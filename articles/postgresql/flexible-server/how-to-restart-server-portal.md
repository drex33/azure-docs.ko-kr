---
title: 다시 시작 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL에서 다시 시작 작업을 수행하는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: e2d82ca519d359b1466e04f79d35848f099b4a16
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268708"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버 다시 시작



이 문서에서는 유연한 서버를 다시 시작하는 단계별 절차를 제공합니다. 이 작업은 데이터베이스 서버를 다시 시작해야 하는 정적 매개 변수 변경 내용을 적용하는 데 유용합니다. 영역 중복 고가용성으로 구성된 서버에 대한 절차도 동일합니다. 

> [!IMPORTANT]
> 고가용성으로 구성된 경우 주 서버와 대기 서버가 동시에 다시 시작됩니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   유연한 서버가 있어야 합니다.

## <a name="restart-your-flexible-server"></a>유연한 서버 다시 시작

유연한 서버를 다시 시작하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 다시 시작할 유연한 서버를 선택합니다.

2.  왼쪽 패널에서 **개요** 를 클릭하고 을 클릭합니다.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="선택 항목 다시 시작":::

3.  팝업 확인 메시지가 표시됩니다.

4.  계속 진행하려면 **예** 를 클릭합니다.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="다시 시작 확인":::
 
6.  다시 시작 작업이 시작되었다는 알림이 표시됩니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
-   [영역 중복 고가용성](./concepts-high-availability.md)에 대한 자세한 정보
