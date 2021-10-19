---
title: Azure Lab Services의 클래스룸 랩용 대시보드 사용 | Microsoft Docs
description: Azure Lab Services의 클래스룸 랩용 대시보드를 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fdc0227a6d0ce6bd46c4157ba0700a06d734928d
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178997"
---
# <a name="dashboard-for-labs"></a>클래스룸 랩용 대시보드
이 문서에서는 Azure Lab Services의 클래스 룸 랩용 대시보드 보기에 대해 설명합니다. 

![Azure Lab Services의 클래스룸 랩 대시보드 보기를 보여주는 스크린샷](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>비용 및 청구 타일
이 타일은 다음과 같은 비용 예측 세부 정보를 제공합니다.

| 설정 | 값 | 
| ------- | ----- | 
| 할당량 시간 | 사용자가 예약된 시간 외에 VM을 사용할 수 있는 최대 시간입니다. |
| 예약된 시간 | 랩에서 설정된 일정에 따라 발생하는 시간입니다. 이 값은 모든 일정 이벤트에 대해 보낸 날짜/종료 날짜가 설정된 경우에만 사용할 수 있습니다. |
| 시간/사용자 | 할당량 시간과 예약된 시간 합계입니다. |
| 최대 사용자 수 | 클레임될 모든 가상 머신을 기준으로 한 랩의 최대 사용자 수입니다. |
| 시간 x 사용자 | 시간/사용자에 사용자 수를 곱합니다. |
| 조정된 할당량 | 특정 사용자에게 추가된 할당량 시간의 합계입니다. |
| 총 시간 * $/시간 | 선택한 VM 크기에 따른 시간당 비용입니다. 이는 일반적인 용량제(Pay As You Go) 가격을 기준으로 합니다. |
| 총 예상 비용 | 현재 설정에 따른 이 랩에 대한 최대 가격입니다. |

## <a name="template-tile"></a>템플릿 타일
이 타일에 다음 정보가 표시됩니다.

- 템플릿이 만들어진 날짜 
- 템플릿이 마지막으로 게시된 날짜 

**템플릿** 페이지로 이동해 클래스에 대한 [템플릿 VM을 관리](how-to-create-manage-template.md)할 수 있는 링크도 있습니다. 

## <a name="virtual-machine-pool-tile"></a>가상 머신 풀 타일

이 타일에 다음 정보가 표시됩니다.

- 학생(사용자)에게 할당된 가상 머신 수
- 아직 학생에게 할당되지 않은 가상 머신 수

**가상 머신 풀** 페이지로 이동해 랩에서 [가상 머신 풀을 관리](how-to-set-virtual-machine-passwords.md)할 수 있게 하는 링크도 있습니다. 

## <a name="users-tile"></a>사용자 타일

이 타일에 다음 정보가 표시됩니다.

- 클래스에 등록된 사용자 수
- 랩에 추가되었지만 클래스에 등록되지 않은 사용자 수 

**사용자** 페이지로 이동해 랩의 [사용자를 관리](how-to-configure-student-usage.md)할 수 있는 링크도 있습니다. 

## <a name="schedules-tile"></a>일정 타일
타일에 현재 랩에 예약된 이벤트가 표시됩니다. **일정** 페이지로 이동해 [일정을 만들고 관리](how-to-create-schedules.md)할 수 있게 하는 링크도 있습니다. 타일에는 두 개의 예약된 이벤트 및 랩에 대한 나머지 예약된 이벤트 수에 대한 세부 정보만 표시됩니다. 

![예약된 이벤트](./media/use-dashboard/scheduled-events.png)

