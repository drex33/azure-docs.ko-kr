---
title: Azure 예약 사용률 문제 해결
description: 이 문서는 Azure Portal에서 없음 또는 0(영) 사용률로 표시되는 Azure 예약을 이해하고 문제를 해결하는 데 도움이 됩니다. 일치하지 않는 사용률도 설명합니다.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 3bade2b547db3b76bde02731807d4e6ef3539711
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649609"
---
# <a name="troubleshoot-reservation-utilization"></a>예약 사용률 문제 해결

이 문서는 Azure Portal에서 없음 또는 0(영) 사용률로 표시되는 Azure 예약을 이해하고 문제를 해결하는 데 도움이 됩니다. 일치하지 않는 사용률도 설명합니다.

## <a name="symptoms"></a>증상

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **예약** 으로 이동합니다.
1. 예약 목록의 **사용률(%)** 열에서 예약 사용률을 확인합니다. 0%일 수 있습니다.
1. 예약을 선택합니다.
1. 예약 개요 페이지에서 그래프의 사용된 백분율이 예약 목록에 표시된 값과 일치하지 않을 수 있습니다.

## <a name="cause"></a>원인

Azure Portal의 **사용률(%)** 열에는 현재 날짜의 값이 표시됩니다. 이 값은 리소스가 실행되는 위치에서 사용량 데이터가 도착하면 계산됩니다. Azure는 사용량을 사용하여 사용률을 계산합니다.

일부 리소스는 사용량을 다른 리소스보다 더 느리게 보고합니다. 또한 SQL Database와 같은 일부 제품 유형은 사용량 데이터를 보고하는 속도가 느립니다.

대기 시간으로 인해 사용률 계산이 실제 사용량보다 낮은 값을 표시할 수 있습니다. 차이는 일 경계에서 뚜렷하게 나타납니다. 이 경우 Azure에서 4~8시간의 사용량 데이터를 가져오지 못하면 0%의 값을 계산합니다. 사용량 데이터가 도착하지 않았으므로 0% 값이 표시되고, 예약에서 리소스에 대한 혜택을 적용하지 않는 것으로 나타납니다.

사용량 데이터가 도착하면 값이 올바른 백분율로 바뀝니다. 모든 사용량 데이터가 수집되면 올바른 값이 결정되어 그래프에 정확하게 표시됩니다.

## <a name="solution"></a>해결 방법

사용률 값이 예상 값과 일치하지 않으면 그래프를 검토하여 실제 사용률을 최대한 많이 확인합니다. 2일보다 더 오래된 요소 값은 정확해야 합니다. 7~30일의 장기 평균은 정확해야 합니다.

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법에 대한 자세한 내용은 [Azure 리소스에 대한 예약 관리](manage-reserved-vm-instance.md)를 참조하세요.