---
title: Azure Virtual Desktop의 사용자별 앱 스트리밍 비용 예상 - Azure
description: Azure Virtual Desktop에 대한 사용자별 청구 비용 예상 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d0218031a79e5271dcdff7531f856094175c477
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799182"
---
# <a name="estimate-per-user-app-streaming-costs-for-azure-virtual-desktop"></a>Azure Virtual Desktop의 사용자별 앱 스트리밍 비용 예상

Azure Virtual Desktop의 사용자별 액세스 가격 책정을 사용하면 Azure Virtual Desktop에 호스팅된 앱 및 데스크톱에 대한 액세스 권한을 조직 외부 사용자에게 부여할 수 있습니다. Azure Virtual Desktop의 사용자별 액세스 가격 책정에 대한 자세한 내용은 [라이선스 및 사용자별 액세스 가격 책정 이해](licensing.md)를 참조하세요. 이 문서에서는 전체 가격 책정을 고려해서 배포에 대한 사용자 액세스 비용을 예상하는 방법을 보여줍니다.

>[!NOTE]
>이 문서의 가격은 프로모션 제안 또는 할인 없이 사용자별 구독의 전체 가격을 기준으로 합니다. 또한 인프라 소비 비용과 같이 Azure Virtual Desktop 배포와 관련된 추가 비용이 있습니다. 이러한 기타 비용에 대한 자세한 내용은 [총 Azure Virtual Desktop 배포 비용 이해](total-costs.md)를 참조하세요.

## <a name="requirements"></a>요구 사항

기존 배포에 대해 사용자별 액세스 비용을 예상하려면 먼저 다음이 필요합니다.

- 이전 달 활성 사용자가 포함된 Azure Virtual Desktop 배포
- [Azure Virtual Desktop용 Azure Monitor 배포](../azure-monitor.md)

## <a name="measure-monthly-user-activity-in-a-host-pool"></a>호스트 풀에서 월간 사용자 활동 측정

호스트 풀 실행을 위한 총 비용을 예상하기 위해서는 먼저 이전 달의 활성 사용자 수를 알아야 합니다. 이 숫자는 Azure Virtual Desktop용 Azure Monitor를 사용하여 알아낼 수 있습니다.

Azure Monitor에서 월간 활성 사용자를 확인하려면 다음 안내를 따르세요.

1. Azure Portal을 열고 **Azure Virtual Desktop** 을 검색하여 선택합니다. 그런 다음, **인사이트** 를 선택하여 Azure Virtual Desktop용 Azure Monitor를 엽니다.

2. 측정하려는 구독 또는 호스트 풀의 이름을 선택합니다.

3. **개요** 탭의 **사용률** 섹션에서 **월간 사용자(MAU)** 차트를 찾습니다.

4. 최근 날짜의 MAU(월간 활성 사용자) 값을 확인합니다. MAU는 해당 날짜 이전 28일 동안 이 호스트 풀에 연결된 사용자 수를 보여줍니다.

## <a name="estimate-per-user-access-costs-for-an-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop 호스트 풀의 사용자별 액세스 비용 예상

그런 다음, 청구 주기별 청구 금액을 확인합니다. 이 숫자는 등록된 구독에서 하나 이상의 세션 호스트에 연결된 사용자 수에 따라 결정됩니다.

또한 사용자 가격 계층은 두 가지입니다.

- RemoteApp 애플리케이션 그룹에만 연결되는 사용자
- 최소한 하나 이상의 데스크톱 애플리케이션 그룹에 연결되는 사용자

구독의 세션 호스트에 연결된 각 가격 책정 계층에 있는 사용자 수를 확인하여 총 비용을 예상할 수 있습니다.

각 계층의 사용자 수를 확인하려면 다음 안내를 따르세요.

1. [Azure Virtual Desktop 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-desktop/)로 이동하고 해당 지역에 대해 “앱” 및 “앱 + 데스크톱” 가격을 찾습니다.
2. [호스트 풀의 월간 활동 측정](#measure-monthly-user-activity-in-a-host-pool)의 4단계에서 찾은 연결 볼륨 숫자를 사용하여 총 사용자 액세스 비용을 계산합니다.
   
   호스트 풀에 RemoteApp 애플리케이션 그룹이 사용되는 경우 “앱”에 있는 가격 값을 연결 볼륨에 곱해야 합니다. 즉, 다음 수식을 사용해야 합니다.

   연결 볼륨 x 사용자별 “앱” 가격 = 총 비용

   호스트 풀에 데스크톱 애플리케이션 그룹이 사용되는 경우에는 대신 다음과 같이 여기에 “앱 + 데스크톱” 사용자별 가격을 곱합니다.

   연결 볼륨 x “앱 + 데스크톱” 사용자별 가격 = 총 비용

>[!IMPORTANT]
>환경에 따라 실제 가격은 이러한 지침에 따라 얻을 수 있는 예상 가격과 크게 다를 수 있습니다. 예를 들어 사용자가 여러 호스트 풀의 리소스에 액세스하지만 각 청구 주기에 사용자별로 한 번만 비용이 청구되어 예상 값이 실제 비용보다 높을 수 있습니다. 데이터 사용 기준이 되는 28일 기간 동안 사용자 활동이 일반적인 월간 사용자 활동과 일치하지 않는 경우에는 예상 값이 실제 비용보다 낮게 나올 수 있습니다. 예를 들어 1주일에 걸친 휴일 또는 주요 서비스 중단 기간이 월에 포함된 경우 사용자 환경이 평균보다 낮아져서 정확한 예상을 제공하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

전체 Azure Virtual Desktop 배포의 총 비용 예상에 대한 자세한 내용은 [총 Azure Virtual Desktop 배포 비용 이해](total-costs.md)를 참조하세요. 라이선스 요구 사항 및 비용에 대한 자세한 내용은 [라이선스 및 사용자별 액세스 가격 책정 이해](licensing.md)를 참조하세요.
