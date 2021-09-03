---
title: 공용 네트워크 액세스 거부 - Azure Portal - Azure Database for MySQL
description: Azure Database for MySQL에 Azure Portal을 사용하여 공용 네트워크 액세스 거부를 구성하는 방법 알아보기
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 9dad137791da3d90fda14382b14d6efbed7ba609
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642166"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL에서 공용 네트워크 액세스 거부

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 모든 공용 구성을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하여 네트워크 보안을 더욱 강화하도록 Azure Database for MySQL 서버를 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

* 범용 또는 메모리 최적화 가격 책정 계층이 포함된 [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>공용 네트워크 액세스 거부 설정

다음 단계를 수행하여 MySQL 서버에서 공용 네트워크 액세스 거부를 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 서버를 선택합니다.

1. MySQL 서버 페이지의 **설정** 에서 **연결 보안** 을 클릭하여 연결 보안 구성 페이지를 엽니다.

1. **공용 네트워크 액세스 거부** 에서 **예** 를 선택하여 MySQL 서버에 대한 공용 액세스 거부를 사용하도록 설정합니다.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG" alt-text="Azure Database for MySQL 네트워크 액세스 거부":::

1. **저장** 을 클릭하여 변경 내용을 저장합니다.

1. 연결 보안 설정이 사용하도록 설정되었음을 확인하는 알림이 나타납니다.

    :::image type="content" source="./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png" alt-text="Azure Database for MySQL 네트워크 액세스 성공 거부":::

## <a name="next-steps"></a>다음 단계

[메트릭에 대한 경고를 만드는 방법](howto-alert-on-metric.md)에 대해 알아봅니다.
