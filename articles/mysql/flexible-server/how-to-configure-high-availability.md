---
title: 영역 중복 고가용성 Azure Portal을 통해 Azure Database for MySQL 유연한 서버 관리
description: 이 문서에서는 Azure Portal를 통해 Azure Database for MySQL 유연한 서버에서 영역 중복 고가용성을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 03597e324449cdb5d0530163c806d950dea5813a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438154"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL 유연한 서버에서 영역 중복 고가용성 관리 

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 유연한 서버에서 영역 중복 고가용성 구성을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다.

고가용성 기능은 서로 다른 영역에서 물리적으로 분리된 주 복제본과 대기 복제본을 프로비저닝합니다. 자세한 내용은 [고가용성 개념 설명서](./concepts/../concepts-high-availability.md)를 참조하세요.

> [!IMPORTANT]
> 유연한 서버를 만드는 동안에만 영역 중복 고가용성을 사용할 수 있습니다.

이 페이지에서는 고가용성을 사용하거나 사용하지 않도록 설정하는 방법에 대한 지침을 제공합니다. 이 작업을 수행해도 VNET 구성, 방화벽 설정 및 백업 보존을 비롯한 다른 설정은 변경되지 않습니다. 마찬가지로 고가용성을 사용하지 않도록 설정하는 것은 온라인 작업이며 애플리케이션 연결 및 작업에 영향을 주지 않습니다.

> [!IMPORTANT]
> 영역 중복 고가용성은 동남 아시아, 미국 서부 2, 서유럽 및 미국 동부와 같이 제한된 지역 집합에서 사용할 수 있습니다.  

## <a name="enable-high-availability-during-server-creation"></a>서버를 만드는 동안 고가용성 사용

이 섹션에서는 특히 HA 관련 필드에 대한 세부 정보를 제공합니다. 유연한 서버를 만드는 동안 고가용성을 배포하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 유연한 서버를 선택하고 **만들기** 를 선택합니다.  **구독**, **리소스 그룹**, **서버 이름**, **지역** 및 기타 필드와 같은 세부 정보를 입력하는 방법에 대한 자세한 내용은 서버 만들기에 대한 방법 문서를 참조하세요.

2. 가용성 옵션에서 **영역 중복 고가용성** 확인란을 선택합니다.

3. 기본 컴퓨팅 및 스토리지를 변경하려면 **서버 구성** 을 선택합니다.

4. 고가용성 옵션을 선택한 경우에는 버스트 가능 계층을 선택할 수 없습니다. **범용** 또는 **메모리 최적화** 컴퓨팅 계층 중 하나를 선택할 수 있습니다.

    > [!IMPORTANT]
    > ***범용** _ 및 _ *_메모리 최적화_** 가격 책정 계층에 대한 영역 중복 고가용성만 지원합니다.

5. 드롭다운에서 원하는 **컴퓨팅 크기** 를 선택합니다.

6. 슬라이딩 막대를 사용하여 GiB에서 **스토리지 크기** 를 선택하고 7일에서 35일 사이의 **백업 보존 기간** 을 선택합니다.   

## <a name="disable-high-availability"></a>고가용성 사용 안 함

영역 중복으로 이미 구성된 유연한 서버에 대해 고가용성을 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 유연한 서버를 선택합니다.

2. 유연한 서버 페이지의 전면 패널에서 **고가용성** 을 선택하여 고가용성 페이지를 엽니다.

3. **영역 중복 고가용성** 확인란을 선택하여 옵션을 사용하지 않도록 설정하고 **저장** 을 선택하여 변경 내용을 저장합니다.

4. HA 비활성화를 확인할 수 있는 확인 대화 상자가 표시됩니다.

5. **HA 사용 안 함** 단추를 선택하여 고가용성을 사용하지 않도록 설정합니다.

6. 고가용성 배포 해제가 진행 중이라는 알림이 표시됩니다.

## <a name="forced-failover"></a>강제 장애 조치(failover)

주 서버에서 유연한 대기 서버로의 강제 장애 조치(failover)를 수행하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 고가용성 기능이 활성화된 기존 Azure Database for MySQL 유연한 서버를 선택합니다.

2. 유연한 서버 페이지의 전면 패널에서 **고가용성** 을 선택하여 고가용성 페이지를 엽니다.

3. **주 가용성 영역** 및 **대기 가용성 영역** 확인

4. 수동 장애 조치(failover) 절차를 시작하려면 **강제 장애 조치(failover)** 를 선택합니다. 팝업에 기본 검사점 및 최근 검사점에 대한 현재 워크로드에 따라 장애 조치(failover) 예상 시간이 표시되면 메시지를 읽고 확인을 선택합니다.

5. 장애 조치(failover)가 진행 중임을 나타내는 알림이 표시됩니다.

6. 대기 서버로의 장애 조치(failover)가 완료되면 알림이 팝업됩니다.

7. 새로운 **주 가용성 영역** 및 **대기 가용성 영역** 을 확인합니다.

![강제 장애 조치(failover) 방법](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>다음 단계

- [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
- [영역 중복 고가용성](./concepts-high-availability.md)에 대한 자세한 정보
