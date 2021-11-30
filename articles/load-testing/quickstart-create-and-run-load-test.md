---
title: '빠른 시작: Azure Load Testing 리소스를 만들고 Azure Portal 대규모 부하 테스트를 실행합니다.'
description: 이 빠른 시작에서는 azure Load Testing 리소스를 만들고 Azure Portal 사용하여 외부 웹 사이트에 대해 대규모 부하 테스트를 실행하는 방법을 보여 줍니다.
services: load-testing
ms.service: load-testing
ms.topic: quickstart
author: ntrogh
ms.author: nicktrog
ms.date: 11/30/2021
ms.custom: template-quickstart
adobe-target: true
ms.openlocfilehash: a18f298cc6c0e2115c44d436b5cf7d0f7c173cd9
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133304132"
---
# <a name="quickstart-create-and-run-a-load-test-with-azure-load-testing-preview"></a>빠른 시작: Azure 부하 테스트 미리 보기를 사용하여 부하 테스트 만들기 및 실행

이 빠른 시작에서는 Azure Portal 사용하여 Azure Load Testing 미리 보기 리소스를 만드는 방법을 설명합니다. 이 리소스를 사용하면 Apache JMeter 스크립트를 사용하여 부하 테스트를 만들고 외부 웹 사이트에 대해 테스트를 실행합니다. 

이 빠른 시작이 완료되면 다른 자습서에 사용할 수 있는 리소스 및 부하 테스트가 제공됩니다. 

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-new-azure-load-testing-resource"></a><a name="create_resource"></a> 새 Azure Load Testing 리소스 만들기

먼저 Azure 부하 테스트 리소스를 만듭니다. 이 리소스는 테스트 계획, 테스트 결과 및 기타 관련 아티팩트 보기 및 관리를 위한 중앙 집중식 위치를 제공하는 최상위 리소스입니다.

부하 테스트 리소스가 이미 있는 경우 이 섹션을 건너뛰고 [부하 테스트 만들기를](#create_test)계속 진행합니다.

부하 테스트 리소스가 아직 없는 경우 지금 만듭니다.

[!INCLUDE [azure-load-testing-create-portal](../../includes/azure-load-testing-create-in-portal.md)]

다음 섹션에서는 Apache JMeter 스크립트를 만듭니다. 기존 스크립트가 이미 있는 경우 부하 [테스트 만들기로](#create_test)건너뛸 수 있습니다.

## <a name="create-an-apache-jmeter-script"></a><a name="jmeter"></a> Apache JMeter 스크립트 만들기

이 섹션에서는 다음 섹션에서 웹 엔드포인트를 부하 테스트하는 데 사용할 샘플 Apache JMeter 스크립트를 만듭니다.

1. 로컬 머신에 *SampleTest.jmx라는* 새 파일을 만듭니다.

    ```powershell
    touch SampleTest.jmx
    ```

1. 텍스트 편집기에서 *SampleTest.jmx를* 열고 파일에 다음 코드 조각을 붙여넣습니다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <jmeterTestPlan version="1.2" properties="5.0" jmeter="5.4.1">
      <hashTree>
        <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="Test Plan" enabled="true">
          <stringProp name="TestPlan.comments"></stringProp>
          <boolProp name="TestPlan.functional_mode">false</boolProp>
          <boolProp name="TestPlan.tearDown_on_shutdown">true</boolProp>
          <boolProp name="TestPlan.serialize_threadgroups">false</boolProp>
          <elementProp name="TestPlan.user_defined_variables" elementType="Arguments" guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
            <collectionProp name="Arguments.arguments"/>
          </elementProp>
          <stringProp name="TestPlan.user_define_classpath"></stringProp>
        </TestPlan>
        <hashTree>
          <kg.apc.jmeter.threads.UltimateThreadGroup guiclass="kg.apc.jmeter.threads.UltimateThreadGroupGui" testclass="kg.apc.jmeter.threads.UltimateThreadGroup" testname="jp@gc - Ultimate Thread Group" enabled="true">
            <collectionProp name="ultimatethreadgroupdata">
              <collectionProp name="1400604752">
                <stringProp name="1567">5</stringProp>
                <stringProp name="0">0</stringProp>
                <stringProp name="48873">30</stringProp>
                <stringProp name="49710">60</stringProp>
                <stringProp name="10">10</stringProp>
              </collectionProp>
            </collectionProp>
            <elementProp name="ThreadGroup.main_controller" elementType="LoopController" guiclass="LoopControlPanel" testclass="LoopController" testname="Loop Controller" enabled="true">
              <boolProp name="LoopController.continue_forever">false</boolProp>
              <intProp name="LoopController.loops">-1</intProp>
            </elementProp>
            <stringProp name="ThreadGroup.on_sample_error">continue</stringProp>
          </kg.apc.jmeter.threads.UltimateThreadGroup>
          <hashTree>
            <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="homepage" enabled="true">
              <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
                <collectionProp name="Arguments.arguments"/>
              </elementProp>
              <stringProp name="HTTPSampler.domain">{your-endpoint-url}</stringProp>
              <stringProp name="HTTPSampler.port"></stringProp>
              <stringProp name="HTTPSampler.protocol">https</stringProp>
              <stringProp name="HTTPSampler.contentEncoding"></stringProp>
              <stringProp name="HTTPSampler.path"></stringProp>
              <stringProp name="HTTPSampler.method">GET</stringProp>
              <boolProp name="HTTPSampler.follow_redirects">true</boolProp>
              <boolProp name="HTTPSampler.auto_redirects">false</boolProp>
              <boolProp name="HTTPSampler.use_keepalive">true</boolProp>
              <boolProp name="HTTPSampler.DO_MULTIPART_POST">false</boolProp>
              <stringProp name="HTTPSampler.embedded_url_re"></stringProp>
              <stringProp name="HTTPSampler.implementation">HttpClient4</stringProp>
              <stringProp name="HTTPSampler.connect_timeout">60000</stringProp>
              <stringProp name="HTTPSampler.response_timeout">60000</stringProp>
            </HTTPSamplerProxy>
            <hashTree/>
          </hashTree>
        </hashTree>
      </hashTree>
    </jmeterTestPlan>
    ```

    이 샘플 Apache JMeter 스크립트는 웹 엔드포인트에 동시에 액세스하는 5명의 가상 사용자의 부하 테스트를 시뮬레이션하고 완료하는 데 2분 미만이 걸립니다.

1. 파일에서 자리 표시자 텍스트(중괄호도 바꾸기)를 *`{your-endpoint-url}`* 사용자 고유의 엔드포인트 URL로 대체합니다.

    > [!IMPORTANT]
    > `https`엔드포인트 URL에 또는 를 포함하지 `http` 마세요.

1. 파일을 저장하고 닫습니다.

다음 섹션에서는 Apache JMeter 스크립트를 사용하여 부하 테스트를 만듭니다.

## <a name="create-a-load-test"></a><a name="create_test"></a> 부하 테스트 만들기

Azure 부하 테스트를 사용하면 Apache JMeter 스크립트를 사용하여 부하 테스트를 만들 수 있습니다. 이 스크립트는 애플리케이션 테스트 계획을 정의합니다. 스크립트에는 웹 엔드포인트, 가상 사용자 수 및 기타 테스트 구성 설정에 대한 정보가 포함되어 있습니다.

다음으로, 기존 Apache JMeter 스크립트를 사용하여 새 부하 테스트를 만듭니다.

1. Azure 부하 테스트 리소스로 이동하여 왼쪽 탐색 창에서 **테스트를** 선택한 **다음, + 새 테스트 만들기를** 선택합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/create-new-test.png" alt-text="Azure 부하 테스트 페이지 및 + 새 테스트 만들기를 보여 주는 스크린샷." :::
    
1. 기본 **사항** 탭에서 **테스트 이름** 및 **테스트 설명** 입력 필드를 입력합니다. 필요에 따라 **만든 후 테스트 실행** 상자를 선택할 수 있습니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/create-new-test-basics.png" alt-text="새 테스트를 만들 때 기본 사항 탭을 보여 주는 스크린샷." :::

1. 테스트 **계획** 탭에서 Apache JMeter 스크립트를 선택한 다음, **업로드** 선택하여 파일을 Azure에 업로드합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/create-new-test-test-plan.png" alt-text="새 테스트를 만들 때 테스트 계획 탭을 보여 주는 스크린샷." :::
    
    > [!NOTE]
    > 추가 Apache JMeter 구성 파일을 선택하고 업로드할 수 있습니다.

1. (선택 사항) 매개 **변수** 탭에서 Apache JMeter 스크립트에 대한 입력 매개 변수를 구성할 수 있습니다.

1. 이 빠른 시작의 경우 **로드** 탭에서 기본값을 그대로 둘 수 있습니다.

    |필드  |기본값  |설명  |
    |---------|---------|---------|
    |엔진 인스턴스     |1         |Apache JMeter 스크립트를 실행하는 병렬 테스트 엔진의 수입니다. |
    
    :::image type="content" source="./media/quickstart-create-and-run-loadtest/create-new-test-load.png" alt-text="새 테스트를 만들 때 로드 탭을 보여 주는 스크린샷." :::

1. (선택 사항) 테스트 **조건** 탭에서 부하 테스트가 실패해야 하는 시기를 결정하는 조건을 구성할 수 있습니다.

1. (선택 사항) **모니터링** 탭에서 서버 쪽 메트릭을 캡처할 Azure 애플리케이션 구성 요소를 지정할 수 있습니다. 이 빠른 시작에서는 Azure 호스팅 애플리케이션을 테스트하지 않습니다.

1. **검토 + 만들기를** 선택하고 모든 설정을 검토한 **다음, 만들기를** 선택하여 부하 테스트를 만듭니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/create-new-test-review.png" alt-text="새 테스트를 만들 때 검토 + 만들기 탭을 보여 주는 스크린샷." :::

다음 섹션에서는 부하 테스트를 실행합니다. 만든 후 테스트 **실행** 상자를 선택한 경우 부하 [테스트 결과 보기를](#view)계속할 수 있습니다.

## <a name="run-the-load-test"></a><a name="run"></a> 부하 테스트 실행

이 섹션에서는 이전 섹션에서 만든 부하 테스트를 실행합니다.

부하 테스트를 만들 때 **만든 후 테스트 실행** 상자를 선택한 경우 부하 테스트 결과 [보기로](#view)건너뛸 수 있습니다.

1. 부하 테스트 리소스로 이동하여 왼쪽 탐색 창에서 **테스트를** 선택한 다음, 이전에 만든 테스트를 선택합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/tests.png" alt-text="테스트 목록을 보여 주는 스크린샷" :::

1. 부하 테스트 세부 정보 화면에서 **테스트** **실행** 또는 실행을 선택합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/run-test.png" alt-text="부하 테스트를 실행하는 방법을 보여 주는 스크린샷" :::

1. **실행** 확인 페이지에서 필요에 따라 테스트 세부 정보를 수정한 다음 실행을 **선택하여** 부하 테스트를 시작합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/run-test-confirm.png" alt-text="실행 확인 페이지를 보여 주는 스크린샷." :::

    > [!TIP]
    > Azure Portal UI에서 언제든지 부하 테스트를 중지할 수 있습니다.

다음 섹션에서는 테스트 실행 세부 정보를 살펴보고 부하 테스트 실행을 모니터링합니다.

## <a name="view-the-load-test-results"></a><a name="view"></a> 부하 테스트 결과 보기

부하 테스트가 실행되는 동안 Azure Load Testing은 클라이언트 쪽 및 서버 쪽 메트릭을 모두 캡처합니다. 이 섹션에서는 대시보드를 사용하여 클라이언트 쪽 메트릭을 모니터링합니다.

1. 테스트 실행 페이지로 이동하고 가장 최근의 테스트 실행을 선택하여 테스트 실행 세부 정보 페이지로 이동합니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/test-runs-after-run.png" alt-text="테스트 실행 목록을 보여 주는 스크린샷." :::

    테스트가 실행되는 동안 스트리밍 클라이언트 쪽 메트릭을 볼 수 있습니다. 기본적으로 데이터는 5초마다 새로 고쳐집니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/test-run-aggregated-by-percentile.png" alt-text="부하 테스트 결과를 보여 주는 스크린샷.":::
        
    > [!NOTE]
    > 이 빠른 시작에서는 외부 웹 사이트에 대한 부하 테스트를 실행했습니다. Azure Load Testing은 Azure 호스팅 애플리케이션에 대해서만 서버 쪽 메트릭을 캡처합니다. 엔드 투 엔드 [예제의 성능 병목 현상을 식별하는 방법에 대한 자습서를](./tutorial-identify-bottlenecks-azure-portal.md) 시도할 수 있습니다.

1. 표시 필터를 변경하여 특정 시간 범위, 결과 백분위수 또는 오류 유형을 볼 수도 있습니다.

    :::image type="content" source="./media/quickstart-create-and-run-loadtest/test-result-filters.png" alt-text="부하 테스트 결과의 필터 조건을 보여 주는 스크린샷.":::
 
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [alt-delete-resource-group](../../includes/alt-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이제 외부 웹 사이트를 부하 테스트하는 데 사용한 Azure Load Testing 리소스가 있습니다.

이 리소스를 사용하여 Azure 호스팅 애플리케이션에서 성능 병목 현상을 식별하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [성능 병목 현상 식별](./tutorial-identify-bottlenecks-azure-portal.md)
