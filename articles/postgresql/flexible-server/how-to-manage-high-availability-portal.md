---
title: 영역 중복 고가용성 관리 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL - 유연한 서버에서 영역 중복 고가용성을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: b3912908f66989c5c2cae312bd3cbe03d59dc08e
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266392"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>유연한 서버에서 영역 중복 고가용성 관리



이 문서에서는 유연한 서버에서 영역 중복 고가용성 구성을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다.

고가용성 기능은 서로 다른 영역에서 물리적으로 분리된 주 복제본과 대기 복제본을 프로비저닝합니다. 자세한 내용은 [고가용성 개념 설명서](./concepts-high-availability.md)를 참조하세요. 유연한 서버를 만들 때 또는 만든 후에 고가용성을 사용하도록 설정할 수 있습니다. 이 페이지에서는 고가용성을 사용하거나 사용하지 않도록 설정하는 방법에 대한 지침을 제공합니다. 이 작업을 수행해도 VNET 구성, 방화벽 설정, 백업 보존을 비롯한 다른 설정은 변경되지 않습니다. 마찬가지로, 고가용성을 사용하거나 사용하지 않도록 설정하는 것은 온라인 작업이며 애플리케이션 연결 및 작업에 영향을 주지 않습니다.

## <a name="pre-requisites"></a>필수 구성 요소

영역 중복 고가용성은 여러 영역이 지원되는 지역에서만 사용할 수 있습니다. 

## <a name="enable-high-availability-during-server-creation"></a>서버를 만드는 동안 고가용성 사용

이 섹션에서는 특히 HA 관련 필드에 대한 세부 정보를 제공합니다. 유연한 서버를 만드는 동안 고가용성을 배포하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 유연한 서버를 선택하고 만들기를 클릭합니다.  **구독**, **리소스 그룹**, **서버 이름**, **지역**, 기타 필드와 같은 세부 정보를 입력하는 방법에 대한 자세한 내용은 서버를 만드는 방법 설명서를 참조하세요.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="구독 및 지역 보기":::

2.  **가용성 영역** 을 선택합니다. 이 옵션은 대기 시간을 줄이기 위해 데이터베이스와 동일한 가용성 영역에 애플리케이션을 배치하려는 경우에 유용합니다. 모든 가용성 영역에 유연한 서버를 배포하려면 **기본 설정 없음** 을 선택합니다.
    ![AZ 선택]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="가용성 영역 선택":::  

3.  가용성 옵션에서 **영역 중복 고가용성** 확인란을 클릭합니다.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="고가용성 확인란":::

4.  기본 컴퓨팅 및 스토리지를 변경하려면 **서버 구성** 을 클릭합니다.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="서버 구성 - 컴퓨팅+스토리지":::  

5.  고가용성 옵션을 선택한 경우에는 버스트 가능 계층을 선택할 수 없습니다. **범용** 또는 **메모리 최적화** 컴퓨팅 계층 중 하나를 선택할 수 있습니다. 그런 다음 드롭다운에서 선택 항목의 **컴퓨팅 크기** 를 선택할 수 있습니다.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="컴퓨팅 계층 선택":::  


6.  슬라이딩 막대를 사용하여 **스토리지 크기** 를 GiB 단위로 선택하고 **백업 보존 기간** 을 7일에서 35일 사이로 선택합니다.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="스토리지 백업"::: 

7. **저장** 을 클릭합니다. 

## <a name="enable-high-availability-post-server-creation"></a>고가용성 게시 서버 만들기 사용

기존의 유연한 서버에 대해 고가용성을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 기존 PostgreSQL 유연한 서버를 선택합니다.

2.  유연한 서버 페이지의 왼쪽 패널에서 **고가용성** 을 클릭하여 고가용성 페이지를 엽니다.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="왼쪽 패널 선택"::: 

3.  **영역 중복 고가용성** 확인란을 클릭하여 옵션을 **사용하도록 설정** 하고 **저장** 을 클릭하여 변경 내용을 저장합니다.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="고가용성 사용"::: 

4.  고가용성을 사용하도록 설정하면 추가 서버 및 스토리지 배포로 인해 비용이 증가한다는 확인 대화 상자가 표시됩니다.

5.  **HA 사용** 단추를 클릭하여 고가용성을 사용하도록 설정합니다.

6.  고가용성 배포가 진행 중이라는 알림이 표시됩니다.

## <a name="disable-high-availability"></a>고가용성 사용 안 함

영역 중복으로 이미 구성된 유연한 서버에 대해 고가용성을 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for PostgreSQL - 유연한 서버를 선택합니다.

2.  유연한 서버 페이지의 전면 패널에서 **고가용성** 을 클릭하여 고가용성 페이지를 엽니다.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="왼쪽 패널 선택"::: 

3.  **영역 중복 고가용성** 확인란을 클릭하여 옵션을 **사용하지 않도록 설정** 합니다. **저장** 을 클릭하여 변경 내용을 저장합니다.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="고가용성 사용 안 함"::: 

4.  고가용성 사용 안 함을 확인할 수 있는 확인 대화 상자가 표시됩니다.

5.  **HA 사용 안 함** 단추를 클릭하여 고가용성을 사용하지 않도록 설정합니다.

6.  고가용성 배포 해제가 진행 중이라는 알림이 표시됩니다.

## <a name="forced-failover"></a>강제 장애 조치(failover)

다음 단계를 수행하여 강제로 주 서버를 유연한 대기 서버로 장애 조치합니다. 그러면 기본 서버가 즉시 중단되고 대기 서버로의 장애 조치가 트리거됩니다. 이러한 기능은 워크로드에 대해 계획되지 않은 중단 장애 조치 시간을 테스트하는 것과 같은 상황에서 유용합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 고가용성 기능이 이미 사용하도록 설정된 기존의 유연한 서버를 선택합니다.
2.  유연한 서버 페이지의 전면 패널에서 고가용성을 클릭하여 고가용성 페이지를 엽니다.
3.  주 가용성 영역 및 대기 가용성 영역 확인
4.  강제 장애 조치를 클릭하여 수동 장애 조치 절차를 시작합니다. 장애 조치가 완료될 때까지 가동 중지 시간이 나타날 수 있다는 팝업 메시지가 표시됩니다. 메시지를 읽고 확인을 클릭합니다.
5.  장애 조치가 진행 중임을 나타내는 알림이 표시됩니다.
6.  대기 서버로의 장애 조치가 완료되면 알림이 팝업됩니다.
7.  새로운 주 가용성 영역 및 대기 가용성 영역을 확인합니다.
    
    :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-forced-failover.png" alt-text="주문형 강제 장애 조치"::: 

>[!IMPORTANT] 
> * 장애 조치를 연속해서 바로 수행하지는 마세요. 장애 조치 간에 최소 15-20분 동안 기다리면 새 대기 서버를 완전히 설정할 수 있습니다.
>
> * 포털에서 보고되는 전체 종단 간 작업 시간은 애플리케이션에서 발생하는 실제 가동 중지 시간보다 길 수 있습니다. 애플리케이션 관점에서 가동 중지 시간을 측정하세요. 

## <a name="planned-failover"></a>계획된 장애 조치

주 서버에서 유연한 대기 서버로의 계획된 장애 조치를 수행하려면 다음 단계를 수행합니다. 여기서는 먼저 대기 서버를 준비하고 장애 조치를 수행합니다. 이렇게 하면 장애 조치 이벤트 후와 같은 상황에서 대기 서버로의 정상 장애 조치를 수행하기 때문에 가동 중지 시간이 최소화됩니다.
1.  [Azure Portal](https://portal.azure.com/)에서 고가용성 기능이 이미 사용하도록 설정된 기존의 유연한 서버를 선택합니다.
2.  유연한 서버 페이지의 전면 패널에서 고가용성을 클릭하여 고가용성 페이지를 엽니다.
3.  주 가용성 영역 및 대기 가용성 영역 확인
4.  계획된 장애 조치를 클릭하여 수동 장애 조치 절차를 시작합니다. 프로세스를 알리는 팝업 메시지가 표시됩니다. 메시지를 읽고 확인을 클릭합니다.
5.  장애 조치가 진행 중임을 나타내는 알림이 표시됩니다.
6.  대기 서버로의 장애 조치가 완료되면 알림이 팝업됩니다.
7.  새로운 주 가용성 영역 및 대기 가용성 영역을 확인합니다.
        :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-planned-failover.png" alt-text="주문형 계획된 장애 조치"::: 

>[!IMPORTANT] 
>
> * 장애 조치를 연속해서 바로 수행하지는 마세요. 장애 조치 간에 최소 15-20분 동안 기다리면 새 대기 서버를 완전히 설정할 수 있습니다.
>
> * 계획된 장애 조치는 활동이 적은 기간에 수행하는 것이 좋습니다.
>
> * 전체 종단 간 작업 시간은 애플리케이션에서 발생하는 실제 가동 중지 시간보다 길 수 있습니다. 애플리케이션 관점에서 가동 중지 시간을 측정하세요.


## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
-   [영역 중복 고가용성](./concepts-high-availability.md)에 대한 자세한 정보
