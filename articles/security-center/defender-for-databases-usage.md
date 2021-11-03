---
title: Microsoft Defender에서 오픈 소스 관계형 데이터베이스에 대 한 경고 설정 및 응답
description: 오픈 소스 관계형 데이터베이스에 대해 Microsoft Defender를 구성 하 여 데이터베이스에 대 한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7d34663e9c83e74ea25ce501a89a5a34b38677e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056011"
---
# <a name="enable-microsoft-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>오픈 소스 관계형 데이터베이스에 대해 Microsoft Defender를 사용 하도록 설정 하 고 경고에 응답

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender는 비정상적인 활동을 감지 하 여 다음 서비스에 대 한 데이터베이스 액세스 또는 악용 시도를 비정상적인 시도를 감지 합니다.

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Microsoft Defender 계획에서 경고를 받으려면 먼저 [아래](#enable-enhanced-security)와 같이 사용 하도록 설정 해야 합니다.

[오픈 소스 관계형 데이터베이스용 Microsoft Defender 소개](defender-for-databases-introduction.md)에서이 microsoft defender 계획에 대해 자세히 알아보세요.

## <a name="enable-enhanced-security"></a>보안 강화 사용

1. [Azure Portal](https://portal.azure.com)에서 보호하려는 데이터베이스 서버의 구성 페이지로 이동합니다.

1. 왼쪽의 보안 메뉴에서 **클라우드 용 Microsoft Defender** 를 선택 합니다.

1. 보안 강화를 사용 하지 않는 경우 다음 스크린샷에 표시 된 것 처럼 단추가 표시 됩니다. [데이터베이스 유형] (예: "Microsoft Defender for MySQL") **에 대해 Microsoft Defender 사용** 을 선택 하 고 **저장** 을 선택 합니다.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="MySQL 용 Microsoft Defender를 사용 하도록 설정 합니다." lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > 포털에서 이 페이지는 데이터베이스 유형(PostgreSQL, MySQL 또는 MariaDB)에 관계없이 동일합니다.

## <a name="respond-to-security-alerts"></a>보안 경고에 대응

데이터베이스에서 Microsoft Defender를 사용 하도록 설정 하면 비정상 작업을 검색 하 고 경고를 생성 합니다. 이러한 경고는 다음을 포함하여 여러 위치에서 사용할 수 있습니다.

- Azure Portal에서 다음을 수행합니다.
    - **Microsoft defender For cloud의 보안 경고 페이지** -볼 수 있는 권한이 있는 구독에서 클라우드에 대해 Defender가 보호 하는 모든 리소스에 대 한 경고를 표시 합니다.
    - 리소스의 **클라우드 용 Microsoft Defender** 페이지-위의 [고급 보안 사용](#enable-enhanced-security)에 나와 있는 것 처럼 특정 리소스에 대 한 경고 및 권장 사항을 보여 줍니다.
- 조직 내 모든 사용자의 받은 편지함이 [이메일 경고를 받도록 지정되었습니다](configure-email-notifications.md).  

> [!TIP]
> [클라우드 개요 대시보드의 Microsoft Defender](overview-page.md) 에서 라이브 타일은 데이터베이스를 포함 하 여 모든 리소스에 대 한 활성 위협의 상태를 추적 합니다. 타일을 선택 하 여 클라우드 경고 용 Defender를 시작 하 고 데이터베이스에서 감지 된 활성 위협에 대 한 개요를 가져옵니다.
>
> 보안 경고에 응답 하는 자세한 단계 및 권장 방법은 [보안 경고에 응답](tutorial-security-incident.md#respond-to-a-security-alert)을 참조 하세요.

### <a name="respond-to-email-notifications-of-security-alerts"></a>보안 경고 이메일 알림에 대한 대응

Defender for Cloud는 비정상적인 데이터베이스 활동을 검색할 때 전자 메일 알림을 보냅니다. 이 이메일에는 비정상적 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간 등, 의심스러운 보안 이벤트에 대한 정보가 포함됩니다. 또한 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치도 제공합니다.

1. 전자 메일에서 **전체 경고 보기** 링크를 선택 하 여 Azure Portal를 시작 하 고 데이터베이스에서 감지 된 활성 위협에 대 한 개요를 제공 하는 경고 페이지를 표시 합니다.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="의심 스러운 무차별 암호 대입 공격에 대 한 클라우드 전자 메일 알림을 위한 Defender":::

    클라우드 포털 페이지의 Defender 내에서 구독 수준의 활성 위협 보기:

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="하나 이상의 구독에 대 한 활성 위협은 Microsoft Defender for Cloud에 표시 됩니다." lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. 현재의 위협을 조사하고 향후의 위협을 조치하기 위한 추가적인 세부 정보와 권장 조치를 보려면 특정 경고를 선택합니다.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="특정 경고의 세부 정보입니다." lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> 경고를 처리하는 방법에 대한 자세한 자습서는 [자습서: 보안 경고 심사, 조사 및 대응](tutorial-security-incident.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [클라우드 트리거에 대 한 Defender에 대 한 응답 자동화](workflow-automation.md)
- [SIEM, SOAR 또는 ITSM 솔루션 경고 스트리밍](export-to-siem.md)
- [클라우드에 대 한 Defender의 경고 표시 안 함](alerts-suppression-rules.md)
