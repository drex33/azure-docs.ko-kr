---
title: 중지/시작 - Azure Portal - Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for MySQL에서 작업을 중지/시작하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e17b788c3852d104d82b456e03c93420f3ea9f3d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467857"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 중지/시작 - 유연한 서버

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 유연한 서버의 중지 및 시작을 수행하는 단계별 절차를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for MySQL 유연한 서버가 있어야 합니다.

## <a name="stop-a-running-server"></a>실행 중인 서버 중지

1.  [Azure Portal](https://portal.azure.com/)에서 중지하려는 유연한 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **중지** 단추를 클릭합니다.

    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="유연한 서버를 중지합니다.":::

3.  **예** 를 클릭하여 서버 중지를 확인합니다.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="유연한 서버 중지를 확인합니다.":::

> [!NOTE]
> 서버가 중지되면 유연한 서버에서 다른 관리 작업을 사용할 수 없습니다.

## <a name="start-a-stopped-server"></a>중지된 서버 시작

1.  [Azure Portal](https://portal.azure.com/)에서 시작하려는 유연한 서버를 선택합니다.

2.  **개요** 페이지의 도구 모음에서 **시작** 단추를 클릭합니다.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="유연한 서버를 시작합니다.":::

> [!NOTE]
> 서버가 시작되면 이제 유연한 서버에서 모든 관리 작업을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아보기
- [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크 만들기 및 관리](./how-to-manage-virtual-network-portal.md).

