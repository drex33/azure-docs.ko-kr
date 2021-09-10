---
title: Container Insights의 보고서
description: Container Insights에서 수집된 데이터를 분석하는 데 사용할 수 있는 보고서에 대해 설명합니다.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a99d7d175f9e79291d4b7c56473939deccae2bff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529139"
---
# <a name="reports-in-container-insights"></a>Container Insights의 보고서
Container Insights의 보고서에는 기본 제공 [Azure 통합 문서](../visualize/workbooks-overview.md)가 권장됩니다. 이 문서에서는 사용할 수 있는 다양한 보고서와 이러한 보고서에 액세스하는 방법을 설명합니다.

## <a name="viewing-reports"></a>보고서 보기
Azure Portal의 **Azure Monitor** 메뉴에서 **컨테이너** 를 선택합니다. **모니터링** 섹션에서 **인사이트** 를 선택하고 특정 클러스터를 선택한 다음, **보고서** 페이지를 선택합니다. 

[![보고서 페이지](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>사용자 지정 통합 문서 만들기
이러한 통합 문서 중 하나를 기준으로 사용자 지정 통합 문서를 만들려면 **통합 문서 보기** 드롭다운을 선택하고 드롭다운 아래에 있는 **AKS 갤러리로 이동** 을 선택합니다. 통합 문서 및 통합 문서 템플릿 사용에 대한 자세한 내용은 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)를 참조하세요.

[![AKS 갤러리](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>노드 통합 문서

- **디스크 용량**: 다음과 같은 관점에서 컨테이너 내의 노드에 표시되는 각 디스크에 대한 대화형 디스크 사용량 차트입니다.

    - 모든 디스크의 디스크 사용 백분율입니다.
    - 모든 디스크의 사용 가능한 디스크 공간입니다.
    - 각 노드의 디스크, 사용된 공간의 백분율, 사용된 공간의 백분율 추세, 사용 가능한 디스크 공간(GiB) 및 사용 가능한 디스크 공간(GiB) 추세를 보여 주는 표 표에서 행을 선택하면 행 아래에 사용된 공간 백분율 및 사용 가능한 디스크 공간(GiB)이 표시됩니다.

- **디스크 IO**: 다음과 같은 관점에서 컨테이너 내의 노드에 표시되는 각 디스크에 대한 대화형 디스크 사용률 차트입니다.

    - 모든 디스크의 디스크 I/O가 읽기 바이트 수/초, 쓰기 바이트 수/초, 읽기 및 쓰기 바이트 수/초 추세별로 요약되어 표시됩니다.
    - 8개의 성능 차트는 디스크 I/O 병목 상태를 측정하고 식별하는 데 도움이 되는 핵심 성과 지표를 표시합니다.

- **GPU**: 각 GPU 인식 Kubernetes 클러스터 노드에 대한 대화형 GPU 사용량 차트입니다.

## <a name="resource-monitoring-workbooks"></a>리소스 모니터링 통합 문서

- **배포**: 사용자 지정 HPA를 포함하여 배포 및 HPA(Horizontal Pod Autoscaler)의 상태입니다. 
  
- **워크로드 세부 정보**: 네임스페이스에 대한 워크로드의 성능 통계를 보여 주는 대화형 차트입니다. 다음과 같은 탭이 포함되어 있습니다.

  - POD별 CPU 및 메모리 사용량의 개요
  - POD 다시 시작 추세, 컨테이너 다시 시작 추세 및 POD의 컨테이너 상태를 보여 주는 POD/컨테이너 상태
  - 컨트롤러에 대한 이벤트의 요약을 보여 주는 Kubernetes 이벤트

- **Kubelet**: 키 노드 작동 통계를 표시하는 두 개의 그리드를 포함합니다.

    - 노드당 개요 그리드는 각 노드에 대한 백분율 및 추세별로 총 작업, 총 오류 및 성공한 작업을 요약해서 보여 줍니다.
    - 작업 유형별 개요는 각 작업에 대해 총 작업, 총 오류, 성공한 작업을 백분율 및 추세로 요약해서 보여 줍니다.
## <a name="billing-workbooks"></a>청구 통합 문서

- **데이터 사용량**: 설명서에서 공유하는 쿼리 라이브러리를 직접 작성하지 않고도 데이터 원본을 시각화할 수 있습니다. 이 통합 문서에는 다음과 같은 관점에서 청구 가능한 데이터를 볼 수 있는 차트가 있습니다.

  - 솔루션별로 수집된 총 청구 가능한 데이터(GB)
  - 컨테이너 로그(애플리케이션 로그)별로 수집된 청구 가능한 데이터
  - Kubernetes 네임스페이스별로 수집된 청구 가능한 컨테이너 로그 데이터
  - 클러스터 이름별로 수집 및 분리된 청구 가능한 컨테이너 로그 데이터
  - 로그 소스 항목별로 수집된 청구 가능한 컨테이너 로그 데이터
  - 진단 마스터 노드 로그별로 수집된 청구 가능한 진단 데이터

## <a name="networking-workbooks"></a>네트워킹 통합 문서

- **NPM 구성**: NPM(네트워크 정책 관리자)을 통해 구성된 네트워크 구성을 모니터링합니다.

  - 전반적인 구성 복잡성에 대한 요약 정보입니다.
  - 시간에 따른 정책, 규칙 및 집합 수를 통해 세 가지 항목 간 관계를 파악하고 구성을 디버그할 시간 차원을 추가할 수 있습니다.
  - 모든 IPSet 및 각 IPSet의 항목 수
  - 네트워크 구성에 구성 요소를 추가하기 위한 노드당 최악 성능 및 평균 성능입니다.

- **네트워크**: 각 노드의 네트워크 어댑터에 대한 대화형 네트워크 사용률 차트입니다. 그리드는 네트워크 어댑터의 성능을 측정하는 데 도움이 되는 핵심 성과 지표를 나타냅니다.



## <a name="next-steps"></a>다음 단계

- Azure Monitor의 통합 문서에 대한 자세한 내용은 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)를 참조하세요.
