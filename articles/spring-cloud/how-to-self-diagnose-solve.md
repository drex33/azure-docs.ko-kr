---
title: Azure Spring Cloud의 자체 진단 및 문제 해결 방법
description: Azure Spring Cloud의 자체 진단 및 문제 해결 방법을 알아봅니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: b6997329b8e0be698d83aa5dddc32a09fb23eafb
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537447"
---
# <a name="self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Azure Spring Cloud의 자체 진단 및 문제 해결

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure Spring Cloud 진단은 구성 없이 앱 문제를 해결하는 대화형 환경입니다. 문제를 발견하면 Azure Spring Cloud 진단에서 문제를 식별하고 해결하는 데 도움이 되는 정보로 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 연습을 완료하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 배포된 Azure Spring Cloud 서비스 인스턴스 [Azure CLI를 통한 앱 배포를 위한 빠른 시작](./quickstart.md)을 수행하여 시작하세요.
* 해당 서비스 인스턴스에서 이미 하나 이상의 애플리케이션이 생성되었습니다.

## <a name="navigate-to-the-diagnostics-page"></a>진단 페이지로 이동

1. Azure Portal에 로그인합니다.
2. Azure Spring Cloud **개요** 페이지로 이동합니다.
3. 왼쪽 탐색 창에서 **진단 및 문제 해결** 을 선택합니다.

![진단, 해결 대화 상자](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

## <a name="search-logged-issues"></a>기록되는 문제 검색

문제를 찾으려면 키워드를 입력하여 검색하거나 솔루션 그룹을 선택하여 해당 범주의 모든 항목을 탐색할 수 있습니다.

![검색 문제](media/spring-cloud-diagnose/search-detectors.png)

**구성 서버 상태 검사**, **구성 서버 상태** 또는 **구성 서버 업데이트 기록** 을 선택하면 다양한 결과가 표시됩니다.

![문제 옵션](media/spring-cloud-diagnose/detectors-options.png)

대상 탐지기를 찾아 선택하여 실행합니다. 탐지기를 실행한 후 진단 요약이 표시됩니다. **전체 보고서 보기** 를 선택하여 진단 정보를 확인하거나 **타일 메뉴 표시** 단추를 선택하여 탐지기 목록으로 돌아갈 수 있습니다.

![진단 요약](media/spring-cloud-diagnose/summary-diagnostics.png)

진단 정보 페이지에서 오른쪽 위 모서리의 컨트롤러를 사용하여 진단 시간 범위를 변경할 수 있습니다. 더 많은 메트릭이나 로그를 보려면 각 진단을 설정/해제합니다. 메트릭 및 로그가 15분 지연될 수 있습니다.

![진단 세부 정보](media/spring-cloud-diagnose/diagnostics-details.png)

일부 결과에는 관련 문서가 포함되어 있습니다.

![관련 세부 정보](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>다음 단계

* [경고 및 작업 그룹을 사용하여 Spring Cloud 리소스 모니터링](./tutorial-alerts-action-groups.md)
* [Azure Spring Cloud Service의 보안 컨트롤](./concept-security-controls.md)
