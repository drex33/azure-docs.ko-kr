---
title: 프라이빗 클라우드 활동 모니터링
titleSuffix: Azure VMware Solution by CloudSimple
description: 경고, 이벤트, 작업 및 감사를 포함하여 Azure VMware Solution by CloudSimple 환경의 활동에 대해 사용할 수 있는 정보를 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bc83c19e18913b08411807c7b60dc6a47572cba9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617476"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>VMware Solution by CloudSimple 활동 모니터링

CloudSimple 활동 로그는 CloudSimple 포털에서 수행된 작업에 대한 인사이트를 제공합니다.  목록에는 경고, 이벤트, 작업 및 감사가 포함됩니다.  활동 로그를 사용하여 누가, 언제, 어떤 작업을 수행했는지 확인합니다.  활동 로그에는 사용자가 수행한 읽기 작업이 포함되지 않습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

[CloudSimple 포털](access-cloudsimple-portal.md)에 액세스합니다.

## <a name="activity-information"></a>활동 정보

활동 페이지에 액세스하려면 사이드 메뉴에서 **활동** 을 선택합니다.

![활동 페이지 개요](media/activity-page-overview.png)

활동 페이지에서 활동에 대한 세부 정보를 보려면 활동을 선택합니다. 오른쪽에 세부 정보 패널이 열립니다. 패널의 작업은 활동 유형에 따라 다릅니다. **X** 를 클릭하여 패널을 닫습니다.

열 헤더를 클릭하여 표시를 정렬합니다.  보려는 특정 값의 열을 필터링 할 수 있습니다.  **CSV로 다운로드** 아이콘을 클릭하여 활동 보고서를 다운로드합니다.

## <a name="alerts"></a>경고

경고는 CloudSimple 환경의 중요한 활동에 대한 알림입니다.  경고에는 청구 또는 사용자 액세스에 영향을 주는 이벤트가 포함됩니다.

경고를 확인하고 목록에서 제거하려면 목록에서 하나 이상을 선택하고 **승인** 을 클릭합니다.

경고에 대해 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭하고 보려는 열을 선택합니다.

| 열 | Description |
------------ | ------------- |
| 경고 유형 | 경고 범주입니다.|
| Time | 경고가 발생한 시간입니다. |
| 심각도 | 경고의 중요도입니다.|
| 리소스 이름 | 프라이빗 클라우드 이름과 같이 리소스에 할당된 이름입니다. |
| 리소스 종류 | 리소스 범주(프라이빗 클라우드, 클라우드 랙)입니다. |
| 리소스 ID | 리소스의 식별자입니다. |
| Description | 경고를 트리거한 항목의 설명입니다. |
| 승인됨 | 경고가 승인되었는지 여부를 나타냅니다. |

## <a name="events"></a>이벤트

이벤트는 CloudSimple 포털에서 사용자 및 시스템 활동을 보여 줍니다. 이벤트 페이지에는 특정 리소스와 관련된 활동 및 영향의 심각도가 나열됩니다.

경고에 대해 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭하고 보려는 열을 선택합니다.

| 열 | Description |
------------ | ------------- |
| Time | 이벤트가 발생한 날짜 및 시간입니다. |
| 이벤트 유형 | 이벤트를 식별하는 숫자 코드입니다. |
| 심각도 | 이벤트 심각도입니다.|
| 리소스 이름 | 프라이빗 클라우드 이름과 같이 리소스에 할당된 이름입니다. |
| 리소스 종류 | 리소스 범주(프라이빗 클라우드, 클라우드 랙)입니다. |
| Description | 경고를 트리거한 항목의 설명입니다. |

## <a name="tasks"></a>작업

작업은 완료하는 데 30초 이상이 소요될 것으로 예상되는 프라이빗 클라우드 활동입니다. (30초 미만이 소요될 것으로 예상되는 활동은 이벤트로만 보고됩니다.) 작업 페이지를 열어 프라이빗 클라우드에 대한 작업 진행 상황을 추적합니다.

경고에 대해 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭하고 보려는 열을 선택합니다.

| 열 | Description |
------------ | ------------- |
| 작업 ID | 작업의 고유 식별자입니다. |
| 작업(Operation) | 작업(task)이 수행하는 작업(Action)입니다. |
| 사용자 | 작업을 완료하기 위해 할당된 사용자입니다. |
| 리소스 이름 | 리소스에 할당된 이름입니다. |
| 리소스 종류 | 리소스 범주(프라이빗 클라우드, 클라우드 랙)입니다. |
| 리소스 ID | 리소스의 식별자입니다. |
| 시작 | 작업 시작 시간입니다. |
| 끝 | 작업 종료 시간입니다. |
| 상태 | 현재 작업 상태입니다. |
| 경과 시간 | 작업을 완료하는 데 걸린 시간(완료된 경우) 또는 현재 걸리는 시간(진행 중인 경우)입니다. |
| Description | 작업 설명입니다. |

## <a name="audit"></a>감사

감사 로그는 사용자 활동을 추적합니다. 감사 로그를 사용하여 모든 사용자의 사용자 활동을 모니터링할 수 있습니다.

경고에 대해 다음 정보 열을 사용할 수 있습니다. **열 편집** 을 클릭하고 보려는 열을 선택합니다.

| 열 | Description |
------------ | ------------- |
| Time | 감사 항목의 시간입니다. |
| 작업(Operation) | 작업(task)이 수행하는 작업(Action)입니다. |
| 사용자 | 작업에 할당된 사용자입니다. |
| 리소스 이름 | 리소스에 할당된 이름입니다. |
| 리소스 종류 | 리소스 범주(프라이빗 클라우드, 클라우드 랙)입니다. |
| 리소스 ID | 리소스의 식별자입니다. |
| 결과 | **성공** 과 같은 활동의 결과입니다. |
| 소요 시간 | 작업을 완료하는 데 걸리는 시간입니다. |
| Description | 액션에 대한 설명입니다. |

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [프라이빗 클라우드](cloudsimple-private-cloud.md)에 대해 자세히 알아보기
