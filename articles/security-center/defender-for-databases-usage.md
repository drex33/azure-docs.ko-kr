---
title: 오픈 소스 관계형 데이터베이스에 대한 Azure Defender 경고 설정 및 대응
description: 오픈 소스 관계형 데이터베이스에 대해 Azure Defender를 구성하여 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 06/17/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1300a8ad11d7091085c95205b435a2d3fb077cd3
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369972"
---
# <a name="enable-azure-defender-for-open-source-relational-databases-and-respond-to-alerts"></a>오픈 소스 관계형 데이터베이스에 Azure Defender 사용하도록 설정하고 경고에 응답

Azure Defender는 다음 서비스에 대해 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지합니다.

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Azure Defender 플랜에서 경고를 받으려면 먼저 [다음과 같이](#enable-azure-defender) 사용하도록 설정해야 합니다.

[오픈 소스 관계형 데이터베이스용 Azure Defender 소개](defender-for-databases-introduction.md)에서 이 Azure Defender 플랜에 대해 자세히 알아보세요.

## <a name="enable-azure-defender"></a>Azure Defender 사용

1. [Azure Portal](https://portal.azure.com)에서 보호하려는 데이터베이스 서버의 구성 페이지로 이동합니다.

1. 왼쪽의 보안 메뉴에서 **Security Center** 를 선택합니다.

1. Azure Defender 사용하도록 설정되지 않은 경우 다음 스크린샷과 같이 단추가 표시됩니다. **Azure Defender for [데이터베이스 유형] 사용** 을 선택하고(예: "Azure Defender for MySQL") **저장** 을 선택합니다.

    :::image type="content" source="media/defender-for-databases-usage/enable-defender-for-mysql.png" alt-text="Azure Defender for MySQL을 사용하도록 설정합니다." lightbox="media/defender-for-databases-usage/enable-defender-for-mysql.png":::

    > [!TIP]
    > 포털에서 이 페이지는 데이터베이스 유형(PostgreSQL, MySQL 또는 MariaDB)에 관계없이 동일합니다.

## <a name="respond-to-security-alerts"></a>보안 경고에 대응

데이터베이스에서 Azure Defender가 사용하도록 설정되면 비정상적인 활동을 검색하고 경고를 생성합니다. 이러한 경고는 다음을 포함하여 여러 위치에서 사용할 수 있습니다.

- Azure Portal에서 다음을 수행합니다.
    - **Azure Defender 보안 경고 페이지** - 볼 수 있는 권한이 있는 구독에서 Azure Defender가 보호하는 모든 리소스에 대한 경고를 표시합니다.
    - 리소스의 **Security Center** 페이지 - 위의 [Azure Defender 사용](#enable-azure-defender)에서 설명한 것처럼 하나의 특정 리소스에 대한 경고와 권장 사항을 표시합니다.
- 조직 내 모든 사용자의 받은 편지함이 [이메일 경고를 받도록 지정되었습니다](security-center-provide-security-contact-details.md).  

> [!TIP]
> [Azure Security Center 개요 대시보드](overview-page.md)의 라이브 타일은 데이터베이스를 포함한 모든 리소스에 대한 활성 위협의 상태를 추적합니다. 타일을 선택하여 Azure Defender 경고 페이지를 시작하고 데이터베이스에서 검색된 활성 위협에 대한 개요를 가져옵니다.
>
> 자세한 단계 및 Azure Defender 경고에 응답하는 권장 방법은 [보안 경고에 대응](tutorial-security-incident.md#respond-to-a-security-alert)을 참조하세요.


### <a name="respond-to-email-notifications-of-security-alerts"></a>보안 경고 이메일 알림에 대한 대응

Azure Defender가 비정상적인 데이터베이스 활동을 감지하면 이메일 알림을 보냅니다. 이 이메일에는 비정상적 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간 등, 의심스러운 보안 이벤트에 대한 정보가 포함됩니다. 또한 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치도 제공합니다.

1. 이메일에서 **전체 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고, 데이터베이스에서 검색된 활성 위협의 개요를 표시하는 경고 페이지를 표시합니다.
    
    :::image type="content" source="media/defender-for-databases-usage/suspected-brute-force-attack-notification-email.png" alt-text="의심되는 무차별 암호 대입 공격에 대한 Azure Defender의 이메일 알림입니다.":::

    Security Center 포털 페이지 안에서, 구독 수준의 활성 위협을 확인합니다.

    :::image type="content" source="media/defender-for-databases-usage/db-alerts-page.png" alt-text="하나 이상의 구독에 대한 활성 위협이 Azure Security Center에 표시됩니다." lightbox="media/defender-for-databases-usage/db-alerts-page.png":::

1. 현재의 위협을 조사하고 향후의 위협을 조치하기 위한 추가적인 세부 정보와 권장 조치를 보려면 특정 경고를 선택합니다.
    
    :::image type="content" source="media/defender-for-databases-usage/specific-alert-details.png" alt-text="특정 경고의 세부 정보입니다." lightbox="media/defender-for-databases-usage/specific-alert-details.png":::


> [!TIP]
> 경고를 처리하는 방법에 대한 자세한 자습서는 [자습서: 보안 경고 심사, 조사 및 대응](tutorial-security-incident.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Security Center 트리거에 대한 응답 자동화](workflow-automation.md)
- [SIEM, SOAR 또는 ITSM 솔루션 경고 스트리밍](export-to-siem.md)
- [Azure Defender의 경고 표시 안 함](alerts-suppression-rules.md)