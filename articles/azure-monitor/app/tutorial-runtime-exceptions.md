---
title: Azure Application Insights를 사용하여 런타임 예외 진단 | Microsoft Docs
description: Azure Application Insights를 사용하여 애플리케이션에서 런타임 예외를 찾고 진단하는 자습서입니다.
ms.topic: tutorial
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 339f57c6c81f62572184c470f0dddac7169eaf7a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129570"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Azure Application Insights를 사용하여 런타임 예외 찾기 및 진단

Azure Application Insights는 애플리케이션에서 원격 분석을 수집하여 런타임 예외를 식별하고 진단하도록 돕습니다.  이 자습서에서는 애플리케이션을 사용하여 이 프로세스를 안내합니다.  다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 예외 추적을 활성화하도록 프로젝트를 수정
> * 애플리케이션의 다양한 구성 요소에 대한 예외 식별
> * 예외 세부 정보 보기
> * 디버깅을 위해 Visual Studio에 예외에 대한 스냅샷 다운로드
> * 쿼리 언어를 사용하여 실패한 요청의 세부 정보 분석
> * 잘못된 코드를 수정하도록 새 작업 항목 만들기


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- 다음 워크로드로 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발
- [Visual Studio 스냅샷 디버거](https://aka.ms/snapshotdebugger)를 다운로드 및 설치합니다.
- [Visual Studio 스냅샷 디버거](../app/snapshot-debugger.md) 사용
- Azure에 .NET 애플리케이션을 배포하고 [Application Insights SDK를 사용하도록 설정](../app/asp-net.md)합니다. 
- 자습서는 애플리케이션에서 예외의 ID를 추적하여 개발 또는 테스트 환경에서 코드를 수정하여 예외를 생성합니다. 

## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다.


## <a name="analyze-failures"></a>실패 분석
Application Insights는 애플리케이션에서 모든 오류를 수집하고 여러 작업 간에 빈도를 볼 수 있도록 하여 가장 높은 영향으로 해당 내용에 집중할 수 있도록 돕습니다.  그런 다음 이러한 실패의 세부 정보를 드릴다운하여 근본 원인을 식별할 수 있습니다.   

1. **Application Insights** 를 선택한 다음, 구독을 선택합니다.  
2. **실패** 패널을 열려면 **조사** 메뉴 아래의 **실패** 를 선택하거나 **실패한 요청** 그래프를 클릭합니다.

    ![실패한 요청](media/tutorial-runtime-exceptions/failed-requests.png)

3. **실패한 요청** 패널은 실패한 요청 수 및 애플리케이션의 각 작업에 대해 영향을 받는 사용자 수를 표시합니다.  사용자가 이 정보를 정렬하여 사용자에게 가장 큰 영향을 주는 이러한 오류를 식별할 수 있습니다.  이 예제에서는 **직원 가져오기/만들기** 및 **고객/세부 정보 가져오기** 는 큰 실패 수 및 영향을 받는 사용자로 인해 조사할 후보일 가능성이 있습니다.  작업을 선택하면 이 작업에 대한 자세한 정보가 오른쪽 패널에 표시됩니다.

    ![실패한 요청 패널](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. 창을 축소하여 실패율이 급증을 나타내는 기간에 대해 확대합니다.

    ![실패한 요청 창](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. 필터링된 결과의 수가 있는 단추를 클릭하여 관련 샘플을 봅니다. "제안된" 샘플에는 모든 구성 요소와 관련된 원격 분석이 있으며, 이러한 샘플 중 어느 하나에 샘플링이 적용되었을 수도 있습니다. 실패한 요청에 대한 세부 정보를 보려면 검색 결과를 클릭합니다.

    ![실패한 요청 샘플](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. 실패한 요청에 대한 세부 정보는 Gantt 차트를 보여 줍니다. 이 차트에서는 전체 트랜잭션 기간의 50% 이상을 차지함으로써 발생한 두 개의 종속성 오류가 이 트랜잭션에 있음을 보여 줍니다. 이 환경은 이 작업 ID와 관련된 분산 애플리케이션의 구성 요소 간에 모든 원격 분석을 제공합니다. [새 환경에 대해 자세히 알아보세요](../app/transaction-diagnostics.md). 항목 중 하나를 선택하여 세부 정보를 볼 수 있습니다. 

    ![실패한 요청 세부 정보](media/tutorial-runtime-exceptions/failed-request-details.png)

7. 또한 작업 세부 정보는 실패를 야기한 것으로 보이는 FormatException을 보여 줍니다.  잘못된 우편 번호 때문임을 확인할 수 있습니다. 디버그 스냅샷을 열어 Visual Studio에서 코드 수준 디버그 정보를 볼 수 있습니다.

    ![예외 세부 정보](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>실패 코드 식별
스냅샷 디버거는 애플리케이션에서 가장 빈번한 예외의 스냅샷을 수집하여 프로덕션에서 해당 근본 원인을 진단하는 데 도움을 줍니다.  포털에서 디버그 스냅샷을 확인하여 호출 스택을 보고 각 호출 스택 프레임에서 변수를 검사할 수 있습니다. 그 후 스냅샷을 다운로드하여 Visual Studio 2019 Enterprise에서 열고 소스 코드를 디버그할 수 있습니다.

1. 예외 속성에서 **디버그 스냅샷 열기** 를 클릭합니다.
2. **디버그 스냅샷** 패널이 요청에 대한 호출 스택과 함께 열립니다.  메서드를 클릭하여 요청 시 모든 지역 변수의 값을 봅니다.  이 예제에서는 맨 위 메서드에서부터 시작하여 값이 없는 지역 변수를 볼 수 있습니다.

    ![디버그 스냅샷](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. 유효한 값을 가진 첫 번째 호출은 **ValidZipCode** 이며 우편 번호가 정수로 변환할 수 없는 문자와 함께 제공된 것을 볼 수 있습니다.  이는 코드에서 수정되어야 하는 오류로 보입니다.

    ![수정해야 하는 코드의 오류를 보여주는 스크린샷.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. 그러면 수정해야 하는 실제 코드를 찾을 수 있는 Visual Studio로 이 스냅샷을 다운로드할 수 있습니다. 이렇게 하려면 **스냅샷 다운로드** 를 클릭합니다.
5. 스냅샷이 Visual Studio로 로드됩니다.
6. 이제 Visual Studio Enterprise에서 예외를 발생시킨 코드 줄을 신속하게 식별하는 디버그 세션을 실행할 수 있습니다.

    ![코드의 예외](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>분석 데이터 사용
Application Insights에 의해 수집된 모든 데이터는 여러 가지 방법으로 데이터를 분석할 수 있도록 하는 다양한 쿼리 언어를 제공하는 Azure Log Analytics에 저장됩니다.  이 데이터를 사용하여 조사 중인 예외를 생성한 요청을 분석할 수 있습니다. 

1. 코드 위의 CodeLens 정보를 클릭하여 Application Insights에서 제공하는 원격 분석을 봅니다.

    ![코드](media/tutorial-runtime-exceptions/codelens.png)

1. **영향 분석** 을 클릭하여 Application Insights Analytics를 엽니다.  영향을 받는 사용자, 브라우저 및 지역과 같은 실패한 요청의 세부 정보를 제공하는 여러 쿼리로 채워집니다.<br><br>![스크린샷은 여러 쿼리가 포함된 Application Insights 창을 보여줍니다.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>작업 항목 추가
Application Insights를 Azure DevOps 또는 GitHub와 같은 추적 시스템에 연결하는 경우 Application Insights에서 직접 작업 항목을 만들 수 있습니다.

1. Application Insights의 **예외 속성** 패널로 돌아옵니다.
2. **새 작업 항목** 을 클릭합니다.
3. **새 작업 항목** 패널이 이미 채워진 예외에 대한 세부 정보와 함께 열립니다.  저장하기 전에 추가 정보를 추가할 수 있습니다.

    ![새 작업 항목](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>다음 단계
이제 런타임 예외를 식별하는 방법을 배웠으므로 성능 문제를 식별하고 진단하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [성능 문제 식별](./tutorial-performance.md)

