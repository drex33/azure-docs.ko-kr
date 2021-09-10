---
title: Azure Virtual Desktop용 Azure Monitor의 새로운 기능
description: Azure Virtual Desktop 에이전트에 대한 새로운 기능 및 제품 업데이트
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450880"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 Azure Monitor의 새로운 기능

이 문서에서는 각 Azure Virtual Desktop용 Azure Monitor의 새 버전에 대해 수행하는 변경 내용을 설명합니다.

현재 사용 중인 Azure Monitor 버전을 확실하게 알 수 없는 경우 Insights 페이지 또는 구성 문서의 오른쪽 아래에서 찾을 수 있습니다. 통합 문서에 액세스하려면 [https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi)로 이동합니다.

## <a name="how-to-read-version-numbers"></a>버전 번호를 읽는 방법

각 Azure Virtual Desktop용 Azure Monitor 버전에는 세 가지 숫자가 있습니다. 각 숫자의 의미는 다음과 같습니다.

- 첫 번째 숫자는 주 버전이며 일반적으로 주 릴리스를 위해 사용됩니다.

- 두 번째 숫자는 부 버전입니다. 부 버전은 새로운 기능 및 사용 중단 통지와 같은 이전 버전과 호환되는 변경 내용입니다.

- 세 번째 숫자는 잘못된 동작이나 버그를 수정하는 작은 변경에 사용되는 패치 버전입니다.

예를 들어 버전 번호가 1.2.31인 릴리스는 첫 번째 주 릴리스, 두 번째 부 릴리스 및 패치 번호 31에 있습니다.

숫자 중 하나를 늘리면 그 뒤의 모든 번호도 변경해야 합니다. 한 릴리스에는 하나의 버전 번호가 있습니다. 그러나 일부 버전 번호는 릴리스를 추적하지 않습니다. 예를 들어 패치 번호는 임의로 지정할 수 있습니다.

## <a name="version-100"></a>Version 1.0.0

릴리스 날짜: 2021년 3월 21일

이 버전에서는 다음과 같이 변경되었습니다.

- 호스트 진단 페이지의 Azure Virtual Desktop 에이전트 이벤트 로그에서 심각한 오류 및 경고에 대한 새로운 시각적 표시기가 도입되었습니다.

- 기본 구성에서 5개의 비용이 드는 프로세스 성능 카운터를 제거했습니다. 자세한 내용은 [Azure Virtual Desktop용 Azure Monitor에 대한 업데이트된 지침](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173)의 블로그 게시물을 참조하세요.

- 구성 통합 문서에 대한 Windows 이벤트 로그 설치 프로세스가 이제 자동화되었습니다.

- 구성 통합 문서는 이제 권장 Windows 이벤트 로그의 자동화된 배포를 지원합니다.

- 이제 구성 통합 문서에서 리소스 그룹의 지역 외부에 있는 세션 호스트의 Log Analytics 에이전트 및 설정-기본 설정 작업 영역을 설치할 수 있습니다.

- 이제 구성 통합 문서에 설치 프로세스에 대한 탭 레이아웃이 있습니다.

- 이 업데이트에 대한 버전 관리를 도입했습니다.

## <a name="next-steps"></a>다음 단계

일반적인 새로운 기능 페이지는 [Azure Virtual Desktop의 새로운 기능](whats-new.md)을 참조하세요.

Azure Virtual Desktop용 Azure Monitor에 대해 자세히 알아보려면 [Azure Virtual Desktop용 Azure Monitor를 사용하여 배포 모니터링](azure-monitor.md)을 참조하세요.
