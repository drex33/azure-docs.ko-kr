---
title: Visual Studio Code에서 Azure Stream Analytics 작업 탐색
description: 이 문서에서는 Azure Stream Analytics 작업을 로컬 프로젝트로 내보내고, 작업을 나열하고, 작업 엔터티를 보는 방법을 보여줍니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 07/21/2021
ms.topic: how-to
ms.openlocfilehash: 85b6dece608f47818f2115d0462dd4a56752581f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536994"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code를 사용하여 Azure Stream Analytics 탐색(미리 보기)

Visual Studio Code용 Azure Stream Analytics 확장은 개발자에게 Stream Analytics 작업을 관리하는 간단한 환경을 제공합니다. Windows, Mac 및 Linux에서 사용할 수 있습니다. Azure Stream Analytics 확장을 통해 다음을 수행할 수 있습니다.

- 작업 [만들기](quick-create-visual-studio-code.md), 시작 및 중지
- 기존 작업을 로컬 프로젝트로 내보내기
- 작업 나열 및 작업 엔터티 보기
- 작업 모니터에서 작업 다이어그램 및 디버그 보기

## <a name="export-a-job-to-a-local-project"></a>로컬 프로젝트로 작업 내보내기

작업을 로컬 프로젝트로 내보내려면 Visual Studio Code의 **Stream Analytics Explorer** 에서 내보낼 작업을 찾습니다. 그런 다음, 프로젝트에 대한 폴더를 선택합니다. 프로젝트는 선택한 폴더로 내보내지고 Visual Studio Code에서 작업을 계속 관리할 수 있습니다. Visual Studio Code를 사용하여 Stream Analytics 작업을 관리하는 방법에 대한 자세한 내용은 Visual Studio Code [빠른 시작](quick-create-visual-studio-code.md)을 참조하세요.

![Visual Studio Code에서 ASA 작업 내보내기](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>작업 나열 및 작업 엔터티 보기

Visual Studio에서 작업 보기를 사용하여 Azure Stream Analytics 작업과 상호 작용할 수 있습니다.


1. Visual Studio Code 활동 표시줄에서 **Azure** 아이콘을 클릭한 다음, **Stream Analytics 노드** 를 확장합니다. 작업은 구독 아래에 표시되어야 합니다.

   ![Stream Analytics Explorer 열기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업 노드를 확장하면 작업 쿼리, 구성, 입력, 출력 및 함수를 열고 볼 수 있습니다. 

3. 작업 노드를 마우스 오른쪽 단추로 클릭하고 **포털에서 작업 보기 열기** 노드를 선택하여 Azure Portal에서 작업 보기를 엽니다.

   ![포털에서 작업 보기 열기](./media/vscode-explore-jobs/open-job-view.png)

## <a name="view-job-diagram-and-debug-in-job-monitor"></a>작업 모니터에서 작업 다이어그램 및 디버그 보기

Visual Studio Code 에서 작업 모니터를 사용하여 Azure Stream Analytics 작업을 보고 문제를 해결할 수 있습니다.

### <a name="view-job-diagram-and-job-summary"></a>작업 다이어그램 및 작업 요약 보기
1. **작업 모니터** 를 선택합니다. 작업 모니터가 나타나고 작업 다이어그램이 자동으로 로드됩니다.
   
   ![작업 모니터 열기](./media/vscode-explore-jobs/open-job-monitor.png)

2.  작업 다이어그램을 보고 **작업 요약** 을 클릭하여 작업의 속성 및 정보를 볼 수 있습니다. 

      ![작업 요약 보기](./media/vscode-explore-jobs/view-jobs-summary.png)

3.  **연결 테스트** 단추를 클릭하여 입력 및 출력에 대한 연결을 테스트할 수 있습니다.

      ![연결을 테스트](./media/vscode-explore-jobs/test-connection.png)

4.  또한 **스크립트 찾기** 단추를 클릭하여 쿼리를 볼 수도 있습니다.
   
      ![쿼리 보기](./media/vscode-explore-jobs/view-query.png)

### <a name="monitor-and-debug-with-metrics"></a>메트릭을 사용하여 모니터링 및 디버그

1.  화살표 단추를 클릭하면 메트릭 패널을 열 수 있습니다.

      ![메트릭 패널 열기](./media/vscode-explore-jobs/open-metrics-panel.png)

2.  차트에 표시된 주요 메트릭을 사용하여 작업과 상호 작용하고 분석할 수 있습니다. 작업 수준 메트릭 또는 노드 수준 메트릭을 보도록 선택할 수 있습니다. 또한 차트에 표시할 메트릭을 결정할 수도 있습니다.

      ![메트릭 보기](./media/vscode-explore-jobs/view-metrics.png)

### <a name="debug-with-diagnostic-logs-and-activity-logs"></a>진단 로그 및 활동 로그를 사용하여 디버그

문제 해결을 위해 작업의 진단 로그 및 활동 로그를 볼 수 있습니다.

1. **진단 로그** 탭을 선택합니다.

   ![진단 로그 보기](./media/vscode-explore-jobs/view-diagnostic-log.png)

2. **활동 로그** 탭을 선택합니다. 

   ![활동 로그 보기](./media/vscode-explore-jobs/view-activity-logs.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code(미리 보기)에서 Azure Stream Analytics 클라우드 작업 만들기](quick-create-visual-studio-code.md)
