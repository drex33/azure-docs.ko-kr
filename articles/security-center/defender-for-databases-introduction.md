---
title: 오픈 소스 관계형 데이터베이스용 Microsoft Defender - 이점 및 기능
description: PostgreSQL, MySQL 및 MariaDB와 같은 오픈 소스 관계형 데이터베이스에 대한 Microsoft Defender 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fd35ef089ad581d596d2958191dd43816441841
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056049"
---
# <a name="introduction-to-microsoft-defender-for-open-source-relational-databases"></a>오픈 소스 관계형 데이터베이스용 Microsoft Defender 소개

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 플랜은 다음과 같은 오픈 소스 관계형 데이터베이스에 대한 위협 방지를 제공합니다.

- [Azure Database for PostgreSQL](../postgresql/index.yml)
- [Azure Database for MySQL](../mysql/index.yml)
- [Azure Database for MariaDB](../mariadb/index.yml)

Defender for Cloud는 데이터베이스에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 나타내는 비정상적인 활동을 감지합니다. 이 플랜을 사용하면 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적인 위협에 간단하게 대처할 수 있습니다.

## <a name="availability"></a>가용성

| 양상                             | 세부 정보                                                                                                                                    |
|------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                     | GA(일반 공급)                                                     |
| 가격 책정:                           | **오픈 소스 관계형 데이터베이스용 Microsoft Defender** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.   |
| PostgreSQL의 보호 버전:  | 단일 서버 - 범용 및 메모리 최적화. 자세한 내용은 [PostgreSQL 가격 책정 계층](../postgresql/concepts-pricing-tiers.md)을 참조하세요.   |
| MySQL의 보호 버전:       | 단일 서버 - 범용 및 메모리 최적화. 자세한 내용은 [MySQL 가격 책정 계층](../mysql/concepts-pricing-tiers.md)을 참조하세요.                        |
| MariaDB의 보호 버전:     | 범용 및 메모리 최적화. 자세한 내용은 [MariaDB 가격 책정 계층](../mariadb/concepts-pricing-tiers.md)을 참조하세요.                      |
| 클라우드:                            | :::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet) |
|                                    |                                                                                                                                            |

## <a name="what-are-the-benefits-of-microsoft-defender-for-open-source-relational-databases"></a>오픈 소스 관계형 데이터베이스용 Microsoft Defender의 이점은 무엇인가요?

Defender for Cloud는 잠재적인 위협을 감지하고 발생 시 대응할 수 있도록 비정상적인 활동에 대한 보안 경고를 제공합니다.

이 플랜을 사용하도록 설정하면 Defender for Cloud는 비정상적인 데이터베이스 액세스 및 쿼리 패턴은 물론 의심스러운 데이터베이스 활동을 감지할 때 경고를 제공합니다.

이러한 경고는 Defender for Cloud의 보안 경고 페이지에 표시되며 다음을 포함합니다.

- 경고를 트리거한 의심스러운 활동의 세부 정보
- 관련 MITRE ATT&CK 전술
- 위협을 조사하고 완화하는 방법에 권장되는 조치
- Microsoft Sentinel을 사용하여 조사를 계속하는 옵션

:::image type="content" source="media/defender-for-databases-introduction/defender-alerts.png" alt-text="오픈 소스 관계형 데이터베이스용 Microsoft Defender로 보호되는 데이터베이스에서 볼 수 있는 몇 가지 보안 경고입니다." lightbox="./media/defender-for-databases-introduction/defender-alerts.png":::

## <a name="what-kind-of-alerts-does-microsoft-defender-for-open-source-relational-databases-provide"></a>오픈 소스 관계형 데이터베이스용 Microsoft Defender는 어떤 종류의 경고를 제공하나요?

다음과 같은 경우 위협 인텔리전스 강화 보안 경고가 트리거됩니다.

- **비정상적인 데이터베이스 액세스 및 쿼리 패턴** - 예: 여러 자격 증명을 사용한 로그인 시도 실패 횟수가 비정상적으로 많은 경우(무차별 암호 대입 시도)
- **의심스러운 데이터베이스 활동** - 예를 들어, 암호화 마이닝 C&C 서버와 통신하여 보안이 뚫린 컴퓨터에서 SQL Server에 액세스하는 합법적인 사용자
- **무차별 암호 대입 공격** – 단순 무차별 암호 대입을 유효한 사용자에 대한 무차별 암호 대입 또는 성공적인 무차별 암호 대입과 분리하는 기능

> [!TIP]
> 데이터베이스 서버에 대한 전체 보안 경고 목록은 [경고 참조 페이지](alerts-reference.md#alerts-osrdb)에서 확인하세요.



## <a name="next-steps"></a>다음 단계

이 문서에서는 오픈 소스 관계형 데이터베이스용 Microsoft Defender에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> [향상된 보호 사용](enable-enhanced-security.md)
