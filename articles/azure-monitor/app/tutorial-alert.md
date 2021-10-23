---
title: Azure Application Insights에서 경고 보내기 | Microsoft Docs
description: Azure Application Insights를 사용하여 애플리케이션에서 오류에 대한 응답으로 경고를 보내는 자습서입니다.
ms.topic: tutorial
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 52510fa531ba43a9e2bb8b7d915847cd551c520a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129589"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Azure Application Insights를 사용하여 애플리케이션 상태에 대한 모니터링 및 경고

Azure Application Insights를 사용하여 애플리케이션을 모니터링하고 사용할 수 없거나 오류가 발생하거나 성능 문제를 겪을 때 경고를 보낼 수 있습니다.  이 자습서에서는 애플리케이션의 가용성을 지속적으로 확인하는 테스트를 만드는 과정을 단계별로 안내합니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션의 응답을 지속적으로 확인하는 가용성 테스트 만들기
> * 문제가 발생한 경우 관리자에게 메일 보내기

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

[Application Insights 리소스](../app/create-new-resource.md)를 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-availability-test"></a>가용성 테스트 만들기

Application Insights에서 가용성 테스트를 사용하여 전 세계 여러 위치에서 애플리케이션을 자동으로 테스트할 수 있습니다.   이 자습서에서는 웹 애플리케이션을 사용할 수 있는지 확인하기 위한 url 테스트를 수행합니다.  또한 해당 세부 작업을 테스트하는 전체 연습을 만들 수도 있습니다. 

1. **Application Insights** 를 선택한 다음, 구독을 선택합니다.  

2. **조사** 메뉴 아래에서 **가용성** 을 선택한 다음, **테스트 만들기** 를 클릭합니다.

    ![가용성 테스트 추가](media/tutorial-alert/add-test-001.png)

3. 테스트에 대한 이름을 입력하고 다른 기본값을 그대로 둡니다.  이렇게 하면 5분마다 다른 지리적 위치 5군데의 애플리케이션 url에 대한 요청이 트리거됩니다.

4. **경고** 를 선택하여 테스트가 실패하는 경우 대처 방법에 대한 세부 정보를 정의할 수 있는 **경고** 드롭다운을 엽니다. **근 실시간** 을 선택하고 상태를 **사용** 으로 설정합니다.

    경고 조건이 충족할 때 전송할 전자 메일 주소를 입력합니다.  경고 조건이 충족할 때 호출할 웹후크의 주소에 선택적으로 입력할 수 있습니다.

    ![테스트 만들기](media/tutorial-alert/create-test-001.png)

5. 테스트 패널로 돌아가서 줄임표를 선택하고, 경고를 편집하여 근 실시간 경고에 대한 구성을 입력합니다.

    ![경고 편집](media/tutorial-alert/edit-alert-001.png)

6. 3보다 크거나 같으면 실패한 위치로 설정합니다. 경고 임계값에 도달하면 누구에게 알림을 보낼 것인지 구성하는 [작업 그룹](../alerts/action-groups.md)을 만듭니다.

    ![경고 UI 저장](media/tutorial-alert/save-alert-001.png)

7. 경고를 구성한 후에는 테스트 이름을 클릭하여 각 위치의 세부 정보를 봅니다. 테스트를 꺾은선형 그래프 또는 산점도 형식으로 표시하여 특정 시간 범위의 성공/실패를 시각화할 수 있습니다.

    ![테스트 세부 정보](media/tutorial-alert/test-details-001.png)

8. 분산형 차트의 점을 클릭하여 테스트의 세부 정보를 드릴다운할 수 있습니다. 이렇게 하면 엔드투엔드 트랜잭션 세부 정보 보기가 시작됩니다. 아래 예제에서는 실패한 요청에 대한 세부 정보를 보여 줍니다.

    ![테스트 결과](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>다음 단계

이제 문제에 대한 경고를 설정하는 방법을 배웠으므로 사용자가 애플리케이션과 상호 작용하는 방법을 분석하는 방법을 알아보는 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [사용자 이해](./tutorial-users.md)

