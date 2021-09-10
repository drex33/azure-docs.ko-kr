---
title: 제한 및 구성 참조 가이드
description: Azure Logic Apps의 제한 및 구성 정보에 대한 참조 가이드
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: 8a43fa4f3516890b4e4eac63d3714fd39bb65225
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538363"
---
# <a name="limits-and-configuration-reference-for-azure-logic-apps"></a>Azure Logic Apps의 제한 및 구성 참조

> Power Automate에 대한 내용은 [Power Automate의 제한 및 구성](/flow/limits-and-config)을 참조하세요.

이 문서에서는 Azure Logic Apps의 제한 및 구성 정보와 관련 리소스를 설명합니다. 논리 앱 워크플로를 만들려면 시나리오, 솔루션 요구 사항, 원하는 기능 및 워크플로를 실행하려는 환경에 따라 **논리 앱** 리소스 종류를 선택합니다.

> [!NOTE]
> 이러한 호스트 환경에는 동일한 제한이 많이 있지만 차이점도 있습니다. 시나리오에서 다른 제한을 요구하는 경우 [Logic Apps 팀에 문의](mailto://logicappspm@microsoft.com)하여 요구 사항을 논의하세요.

다음 표에는 원래 **논리 앱(소비)** 리소스 종류와 **논리 앱(표준)** 리소스 종류 간의 차이점이 간략하게 요약되어 있습니다. 또한 *단일 테넌트* 환경과 *다중 테넌트* 환경 및 *ISE(통합 서비스 환경)* 의 논리 앱 워크플로 배포, 호스트 및 실행 방법도 비교합니다.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## <a name="workflow-definition-limits"></a>워크플로 정의 제한

다음 표에는 단일 워크플로 정의 값이 나와 있습니다.

| 속성 | 제한 | 메모 |
| ---- | ----- | ----- |
| 구독당 지역별 워크플로 | 워크플로 1,000개 ||
| 워크플로당 트리거 | 트리거 10개 | 이 제한은 디자이너가 아닌 코드 보기 또는 ARM(Azure Resource Manager) 템플릿에서 JSON 워크플로 정의를 작업하는 경우에만 적용됩니다. |
| 워크플로당 작업 | 작업 500개 | 이 제한을 확장하려면 필요에 따라 중첩된 워크플로를 사용하면 됩니다. |
| 작업 중첩 깊이 | 작업 8개 | 이 제한을 확장하려면 필요에 따라 중첩된 워크플로를 사용하면 됩니다. |
| 트리거 또는 작업 - 최대 이름 길이 | 80자 ||
| 트리거 또는 작업 - 최대 입력 또는 출력 크기 | 104,857,600바이트 <br>(105MB) | 단일 테넌트 모델에서 기본 제한을 편집하는 방법에 대한 자세한 내용은 [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](edit-app-settings-host-settings.md)을 참조하세요. |
| 작업 - 결합된 입력 및 출력 최대 크기 | 209,715,200바이트 <br>(210MB) ||
| 식 문자 제한 | 8,192자 ||
| `description` - 최대 길이 | 256자 ||
| `parameters` - 최대 항목 수 | 매개 변수 50개 ||
| `outputs` - 최대 항목 수 | 출력 10개 ||
| `trackedProperties` - 최대 크기 | 8,000자 ||
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>실행 기간 및 보존 기록 제한

다음 표에는 단일 워크플로 실행 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 스토리지의 실행 기록 보존 | 90일 | 90일 | 366일 | 실행이 시작된 후 스토리지에 워크플로 실행 기록을 유지하는 시간입니다. <p>**참고**: 워크플로의 실행 기간이 보존 한도를 초과하면 해당 실행은 스토리지의 실행 기록에서 제거됩니다. 보존 한도에 도달한 후에 즉시 실행을 제거하지 않으면 실행은 7일 이내에 제거됩니다. <p><p>실행이 완료되었든 시간 초과되었든, 실행 기록 보존은 항상 실행 시작 시간과 워크플로 설정인 [**실행 기록 보존(일)** ](#change-retention)에 지정된 현재 한도를 사용하여 계산됩니다. 이전 제한과 상관없이 항상 현재 제한은 항상 보존 계산에 사용됩니다. 자세한 내용은 [스토리지의 기간 및 실행 기록 보존 변경](#change-retention)을 참조하세요. <p><p>**팁**: 시나리오에서 다른 제한을 요구하는 경우 [Logic Apps 팀에 문의](mailto://logicappspm@microsoft.com)하여 요구 사항을 논의하세요. |
| 실행 기간 | 90일 | - 상태 저장 워크플로: 90일 <p><p>- 상태 비저장 워크플로: 5분 | 366일 | 시간 제한을 적용하기 전에 워크플로를 계속 실행할 수 있는 시간입니다. <p>실행 기간은 실행 시작 시간과 시작할 때 워크플로 설정인 [**실행 기록 보존(일)**](#change-duration)에서 지정한 제한을 통해 계산됩니다. <p>**중요**: 실행 기간 값이 항상 스토리지의 실행 기록 보존 값보다 작거나 같은지 확인하세요. 그렇지 않으면 연결된 작업이 완료되기 전에 실행 기록이 삭제될 수 있습니다. <p><p>자세한 내용은 [스토리지의 실행 기간 및 실행 기록 보존 변경](#change-duration)을 참조하세요. <p><p>**팁**: 시나리오에서 다른 제한을 요구하는 경우 [Logic Apps 팀에 문의](mailto://logicappspm@microsoft.com)하여 요구 사항을 논의하세요. |
| 되풀이 간격 | - 최소: 1초 <p><p>- 최대: 500일 | - 최소: 1초 <p><p>- 최대: 500일  | - 최소: 1초 <p><p>- 최대: 500일 ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>스토리지의 실행 기간 및 기록 보존 변경

디자이너에서 동일한 설정이 워크플로가 실행될 수 있는 최대 일 수와 실행 기록을 스토리지에 유지하는 최대 일 수를 제어합니다.

* 다중 테넌트 서비스의 경우 90일 기본 제한은 최대 제한과 동일합니다. 이 값을 줄이는 것만 가능합니다.

* 단일 테넌트 서비스의 경우 90일 기본 제한을 줄이거나 늘릴 수 있습니다. 자세한 내용은 [Visual Studio Code를 사용하여 단일 테넌트 Azure Logic Apps의 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md)를 참조하세요.

* 통합 서비스 환경의 경우 90일 기본 제한을 늘리거나 줄일 수 있습니다.

> [!TIP]
> 시나리오에서 다른 제한을 요구하는 경우 [Logic Apps 팀에 문의](mailto://logicappspm@microsoft.com)하여 요구 사항을 논의하세요.

예를 들어, 보존 제한을 90일에서 30일로 줄였다고 가정합니다. 60일 된 실행은 실행 기록에서 제거됩니다. 보존 기간을 30일에서 60일로 늘리면 20일 된 실행은 40일 더 실행 기록에 보존됩니다.

> [!IMPORTANT]
> 실행 기간이 현재 실행 기록 보존 제한을 초과하면 실행은 스토리지의 실행 기록에서 제거됩니다. 실행 기록 손실을 방지하려면 보존 제한이 *항상* 실행의 가능한 가장 긴 기간보다 길어야 합니다.

이러한 속성의 기본값이나 현재 제한을 변경하려면 다음 단계를 수행합니다.

#### <a name="portal-multi-tenant-service"></a>[Portal(다중 테넌트 서비스)](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com) 검색 상자에서 **논리 앱** 을 찾아 선택합니다.

1. Logic Apps 디자이너에서 논리 앱을 찾고 엽니다.

1. 논리 앱 메뉴에서 **워크플로 설정** 을 선택합니다.

1. **런타임 옵션** 아래의 **실행 기록 보존 기간(일)** 목록에서 **사용자 지정** 을 선택합니다.

1. 슬라이더를 끌어 원하는 일수로 변경합니다.

1. 마쳤으면 **워크플로 설정** 도구 모음에서 **저장** 을 선택합니다.

#### <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

Azure Resource Manager 템플릿을 사용하는 경우 이 설정은 [Microsoft.Logic 워크플로 템플릿 참조](/azure/templates/microsoft.logic/workflows)에 설명된 워크플로 리소스 정의에 속성으로 표시됩니다.

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## <a name="looping-concurrency-and-debatching-limits"></a>루프, 동시성 및 분리 제한

다음 표에는 단일 워크플로 실행 값이 나와 있습니다.

### <a name="loop-actions"></a>루프 작업

<a name="for-each-loop"></a>

#### <a name="for-each-loop"></a>For each 루프

다음 표에는 **For each** 루프 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 배열 항목 | 항목 100,000개 | - 상태 저장 워크플로: 항목 100,000개 <p><p>- 상태 비저장 워크플로: 항목 100개 | 항목 100,000개 | **For each** 루프에서 처리할 수 있는 배열 항목 수입니다. <p><p>더 큰 배열을 필터링하려면 [쿼리 작업](logic-apps-perform-data-operations.md#filter-array-action)을 사용하면 됩니다. |
| 동시 반복 | 동시성 꺼짐: 20 <p><p>동시성 켜짐: <p>- 기본값: 20 <br>- 최소: 1 <br>- 최대: 50 | 동시성 꺼짐: 20 <p><p>동시성 켜짐: <p><p>- 기본값: 20 <br>- 최소: 1 <br>- 최대: 50 | 동시성 꺼짐: 20 <p><p>동시성 켜짐: <p>- 기본값: 20 <br>- 최소: 1 <br>- 최대: 50 | 동시에 또는 병렬로 실행할 수 있는 **For each** 루프 반복 횟수입니다. <p><p>다중 테넌트 서비스에서 이 값을 변경하려면 [**For each** 동시성 제한 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) 또는 [**For each** 루프를 순차적으로 실행](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)을 참조하세요. |
||||||

<a name="until-loop"></a>

#### <a name="until-loop"></a>Until 루프

다음 표에는 **Until** 루프 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 반복 횟수 | - 기본값: 60 <br>- 최소: 1 <br>- 최대: 5,000 | 상태 저장 워크플로: <p><p>- 기본값: 60 <br>- 최소: 1 <br>- 최대: 5,000 <p><p>상태 비저장 워크플로: <p><p>- 기본값: 60 <br>- 최소: 1 <br>- 최대: 100 | - 기본값: 60 <br>- 최소: 1 <br>- 최대: 5,000 | 워크플로 실행 중에 **Until** 루프에 있을 수 있는 주기 수입니다. <p><p>이 값을 변경하려면 **Until** 루프 모양에서 **제한 변경** 을 선택하고 **Count** 속성 값을 지정합니다. |
| 제한 시간 | 기본값: PT1H(1시간) | 상태 저장 워크플로: PT1H(1시간) <p><p>상태 비저장 워크플로: PT5M(5분) | 기본값: PT1H(1시간) | **Until** 루프가 종료되기 전에 실행될 수 있는 시간이며 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)으로 지정됩니다. 시간 제한 값은 각 루프 주기에 대해 평가됩니다. 루프의 작업이 시간 제한보다 오래 걸리면 현재 주기가 중지되지 않습니다. 그러나 제한 조건이 충족되지 않으면 다음 주기가 시작되지 않습니다. <p><p>이 값을 변경하려면 **Until** 루프 모양에서 **제한 변경** 을 선택하고 **Timeout** 속성 값을 지정합니다. |
||||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>동시성 및 개별 처리

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 트리거 - 동시 실행 | 동시성 꺼짐: 무제한 <p><p>동시성 켜짐(되돌릴 수 없음): <p><p>- 기본값: 25 <br>- 최소: 1 <br>- 최대: 100 | 동시성 꺼짐: 무제한 <p><p>동시성 켜짐(되돌릴 수 없음): <p><p>- 기본값: 25 <br>- 최소: 1 <br>- 최대: 100 | 동시성 꺼짐: 무제한 <p><p>동시성 켜짐(되돌릴 수 없음): <p><p>- 기본값: 25 <br>- 최소: 1 <br>- 최대: 100 | 트리거가 동시에 또는 병렬로 시작할 수 있는 동시 실행 횟수입니다. <p><p>**참고**: 동시성이 켜져 있으면 [배열 분리](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)에 대한 **SplitOn** 제한이 항목 100개로 줄어듭니다. <p><p>다중 테넌트 서비스에서 이 값을 변경하려면 [트리거 동시성 제한 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)이나 [인스턴스를 순차적으로 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger)를 참조하세요. |
| 최대 대기 중인 실행 | 동시성 꺼짐: <p><p>- 최소: 실행 1회 <p>- 최대: 실행 50회 <p><p>동시성 켜짐: <p><p>- 최소: 실행 10회 + 동시 실행 횟수 <p>- 최대: 실행 100회 | 동시성 꺼짐: <p><p>- 최소: 실행 1회 <p>- 최대: 실행 50회 <p><p>동시성 켜짐: <p><p>- 최소: 실행 10회 + 동시 실행 횟수 <p>- 최대: 실행 100회 | 동시성 꺼짐: <p><p>- 최소: 실행 1회 <p>- 최대: 실행 50회 <p><p>동시성 켜짐: <p><p>- 최소: 실행 10회 + 동시 실행 횟수 <p>- 최대: 실행 100회 | 현재 워크플로 인스턴스가 이미 최대 동시 인스턴스로 실행된 경우 실행될 때까지 기다릴 수 있는 워크플로 인스턴스 수입니다. <p><p>다중 테넌트 서비스에서 이 값을 변경하려면 [실행 대기 제한 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)을 참조하세요. |
| **SplitOn** 항목 | 동시성 꺼짐: 항목 100,000개 <p><p>동시성 켜짐: 항목 100개 | 동시성 꺼짐: 항목 100,000개 <p><p>동시성 켜짐: 항목 100개 | 동시성 꺼짐: 항목 100,000개 <p><p>동시성 켜짐: 항목 100개 | 배열을 반환하는 트리거의 경우 **For each** 루프를 사용하는 대신 [배열 항목을 여러 워크플로 인스턴스로 분할 또는 분리](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)하여 처리하는 **SplitOn** 속성을 사용하는 식을 지정할 수 있습니다. 이 식은 각 배열 항목에 대한 워크플로 인스턴스를 만들고 실행하는 데 사용되는 배열을 참조합니다. <p><p>**참고**: 동시성이 켜져 있으면 **SplitOn** 제한이 항목 100개로 줄어듭니다. |
||||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>처리량 한도

다음 표에는 단일 워크플로 정의 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 메모 |
|------|--------------|---------------|-------|
| 작업 - 5분 롤링 간격으로 실행 | 기본값: 실행 100,000회 <br>- 높은 처리량 모드: 실행 300,000회 | None | 기본값을 워크플로의 최댓값으로 높일 수 있습니다. 자세한 내용은 미리 보기 상태인 [높은 처리량 모드에서 실행](#run-high-throughput-mode)을 참조하세요. 또는 필요에 따라 [워크플로 두 개 이상에서 워크로드를 배포](handle-throttling-problems-429-errors.md#logic-app-throttling)하면 됩니다. |
| 작업 - 동시 아웃바운드 호출 | 최대 호출 2,500회 | None | 필요에 따라 동시 요청 수를 줄이거나 기간을 단축할 수 있습니다. |
| 관리형 커넥터 제한 | 제한 한도는 커넥터에 따라 다릅니다. | 제한 한도는 커넥터에 따라 다릅니다. | 다중 테넌트의 경우 [각 관리형 커넥터의 기술 참조 페이지](/connectors/connector-reference/connector-reference-logicapps-connectors)를 참조하세요. <p><p>커넥터 제한을 처리하는 방법에 대한 자세한 내용은 [제한 문제 처리(“429 - 너무 많은 요청” 오류)](handle-throttling-problems-429-errors.md#connector-throttling)를 참조하세요. |
| 런타임 엔드포인트 - 동시 인바운드 호출 | 최대 호출 1,000회 | None | 필요에 따라 동시 요청 수를 줄이거나 기간을 단축할 수 있습니다. |
| 런타임 엔드포인트 - 5분당 읽기 호출  | 읽기 호출 60,000회 | None | 이 제한은 워크플로 실행 기록에서 원시 입력 및 출력을 가져오는 호출에 적용됩니다. 필요에 따라 워크플로 두 개 이상에서 워크로드를 배포할 수 있습니다. |
| 런타임 엔드포인트 - 5분당 Invoke 호출 | Invoke 호출 45,000회 | None | 필요에 따라 워크플로 두 개 이상에서 워크로드를 배포할 수 있습니다. |
| 5분당 콘텐츠 처리량 | 600MB | None | 필요에 따라 워크플로 두 개 이상에서 워크로드를 배포할 수 있습니다. |
|||||

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>높은 처리량 모드에서 실행

단일 워크로드 정의의 경우 5분마다 실행되는 작업 수에는 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)이 있습니다. 기본값을 기본값 3배인 워크로드 [최댓값](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)으로 올리려면 높은 처리량 모드(미리 보기)를 사용하면 됩니다. 또는 필요에 따라 [워크플로 두 개 이상에서 워크로드를 배포](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling)하면 됩니다.

#### <a name="portal-multi-tenant-service"></a>[Portal(다중 테넌트 서비스)](#tab/azure-portal)

1. Azure Portal에서 논리 앱 메뉴의 **설정** 아래에 있는 **워크플로 설정** 을 선택합니다.

1. **런타임 옵션** > **높은 처리량** 에서 설정을 **켜기** 로 변경합니다.

   !["워크플로 설정" 및 "높은 처리량"이 "켜기"로 설정된 Azure Portal의 논리 앱 메뉴를 보여주는 스크린샷.](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

논리 앱을 배포하기 위해 ARM 템플릿에서 이 설정을 사용하려면 논리 앱의 리소스 정의에 대한 `properties` 개체에서 `operationOptions` 속성이 `OptimizedForHighThroughput`로 설정된 `runtimeConfiguration` 개체를 추가합니다.

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

논리 앱 리소스 정의에 대한 자세한 내용은 [개요: Azure Resource Manager 템플릿을 사용하여 Azure Logic Apps 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)를 참조하세요.

---

### <a name="integration-service-environment-ise"></a>ISE(통합 서비스 환경)

* [개발자 ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): 분당 최대 500회 실행을 제공하지만, 다음 사항에 유의하세요.

  * 이 SKU는 프로덕션 또는 성능 테스트가 아닌 탐색, 실험, 개발 또는 테스트에만 사용해야 합니다. 이 SKU에는 SLA(서비스 수준 계약), 스케일 업 기능 또는 재생 중 중복도가 없습니다. 즉, 지연 또는 가동 중지 시간이 발생할 수 있습니다.

  * 백 엔드 업데이트로 서비스가 일시적으로 중단될 수 있습니다.

* [프리미엄 ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): 다음 표에서는 이 SKU의 처리량 제한을 설명하지만, 정상적인 처리에서 이러한 제한을 초과하거나 이러한 제한을 초과할 수 있는 부하 테스트를 실행하려면 [Logic Apps 팀에 문의](mailto://logicappsemail@microsoft.com)하여 요구 사항에 대한 도움을 받으세요.

  | 속성 | 제한 | 메모 |
  |------|-------|-------|
  | 기준 단위 실행 제한 | 인프라 용량이 80%에 도달하면 시스템 제한 | 분당 ~4000개 작업 실행을 제공하며 이를 월 단위로 환산하면 매월 ~1억 6000만 개의 작업 실행 제공 |
  | 배율 단위 실행 제한 | 인프라 용량이 80%에 도달하면 시스템 제한 | 각 배율 단위는 분당 추가 작업 실행 횟수를 최대 2000회까지 제공하며 이는 매월 추가 작업 실행 횟수 최대 8000만 회에 해당 |
  | 추가할 수 있는 최대 배율 단위 | 배율 단위 10개 | |
  ||||

<a name="gateway-limits"></a>

## <a name="data-gateway-limits"></a>데이터 게이트웨이 제한

Azure Logic Apps는 온-프레미스 데이터 게이트웨이를 통해 삽입 및 업데이트가 포함된 쓰기 작업을 지원합니다. 그러나 이러한 작업에는 [페이로드 크기 제한](/data-integration/gateway/service-gateway-onprem#considerations)이 있습니다.

<a name="variables-action-limits"></a>

## <a name="variables-action-limits"></a>변수 작업 제한

다음 표에는 단일 워크플로 정의 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 워크플로당 변수 | 변수 250개 | 변수 250개 | 변수 250개 ||
| 변수 - 최대 콘텐츠 크기 | 104,857,600자 | 상태 저장 워크플로: 104,857,600자 <p><p>상태 비저장 워크플로: 1024자 | 104,857,600자 ||
| 변수(배열 형식) - 최대 배열 항목 수 | 항목 100,000개 | 항목 100,000개 | 프리미엄 SKU: 항목 100,000개 <p><p>개발자 SKU: 항목 5000개 ||
||||||

<a name="http-limits"></a>

## <a name="http-request-limits"></a>HTTP 요청 한도

다음 표에는 단일 인바운드 또는 아웃바운드 호출 값이 나와 있습니다.

<a name="http-timeout-limits"></a>

### <a name="timeout-duration"></a>시간 제한 기간

기본적으로 HTTP 작업 및 APIConnection 작업은 [표준 비동기 작업 패턴](/azure/architecture/patterns/async-request-reply)을 따르고 Response 작업은 *동기 작업 패턴* 을 따릅니다. 일부 관리형 커넥터 작업은 비동기 호출을 하거나 웹후크 요청을 수신 대기하므로 이러한 작업에 대한 시간 제한이 다음 제한보다 길어질 수 있습니다. 자세한 내용은 [각 커넥터의 기술 참조 페이지](/connectors/connector-reference/connector-reference-logicapps-connectors)와 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 문서를 참조하세요.

> [!NOTE]
> 단일 테넌트 모델의 **논리 앱(표준)** 리소스 종류의 경우 상태 비정상 워크플로는 *동기식* 으로만 실행될 수 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 아웃바운드 요청 | 120초 <br>(2분) | 230초 <br>(3.9분) | 240초 <br>(4분) | 아웃바운드 요청의 예로는 HTTP 트리거 또는 작업에 의한 호출이 있습니다. <p><p>**팁**: 장기 작업의 경우 [비동기 폴링 패턴](../logic-apps/logic-apps-create-api-app.md#async-pattern)이나 [“Until” 루프](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)를 사용하세요. [호출 가능 엔드포인트](logic-apps-http-endpoint.md)가 있는 또 다른 워크플로를 호출할 때 시간 제한을 해결하려면 기본 제공 Azure Logic Apps 작업을 대신 사용하면 됩니다. **기본 제공** 아래에 있는 디자이너 작업 선택기에서 이 작업을 찾을 수 있습니다. |
| 인바운드 요청 | 120초 <br>(2분) | 230초 <br>(3.9분) | 240초 <br>(4분) | 인바운드 요청의 예로는 요청 트리거, HTTP Webhook 트리거 및 HTTP Webhook 작업에서 수신한 호출이 있습니다. <p><p>**참고**: 원래 호출자가 응답을 받으려면 또 다른 중첩된 워크플로를 호출하지 않는 한 응답의 모든 단계가 제한 내에서 완료되어야 합니다. 자세한 내용은 [논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. |
||||||

<a name="message-size-limits"></a>

### <a name="messages"></a>메시지

| 이름 | 청크 사용됨 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| 콘텐츠 다운로드 - 최대 요청 수 | 예 | 요청 1,000회 | 요청 1,000회 | 요청 1,000회 ||
| 메시지 크기 | No | 100MB | 100MB | 200MB | 이 제한을 해결하려면 [청킹이 있는 대용량 메시지 처리](../logic-apps/logic-apps-handle-large-messages.md)를 참조하세요. 그러나 일부 커넥터 및 API에서는 청크나 심지어 기본 제한을 지원하지 않습니다. <p><p>- AS2, X12 및 EDIFACT 같은 커넥터는 자체적인 [B2B 메시지 제한](#b2b-protocol-limits)이 있습니다. <p>- ISE 커넥터는 비 ISE 커넥터 제한이 아닌 ISE 제한을 사용합니다. |
| 메시지 크기 | 예 | 1GB | 1,073,741,824바이트 <br>(1GB) | 5GB | 이 제한은 기본적으로 청크 분할을 지원하거나 런타임 구성에서 청크 분할을 사용할 수 있는 작업에 적용됩니다. <p><p>ISE를 사용하는 경우 Logic Apps 엔진은 이 제한을 지원하지만, 커넥터 자체의 최대 청크는 엔진 제한으로 제한되어 있습니다. 예제는 [Azure Blob Storage 커넥터의 API 참조](/connectors/azureblob/)를 확인하세요. 청크 분할에 대한 자세한 내용은 [청크 분할을 사용하여 큰 메시지 처리](../logic-apps/logic-apps-handle-large-messages.md)를 참조하세요. |
| 콘텐츠 청크 크기 | 예 | 커넥터별로 다름 | 52,428,800바이트(52MB) | 커넥터별로 다름 | 이 제한은 기본적으로 청크 분할을 지원하거나 런타임 구성에서 청크 분할을 사용할 수 있는 작업에 적용됩니다. |
|||||||

### <a name="character-limits"></a>문자 제한

| 속성 | 제한 | 메모 |
|------|-------|-------|
| 식 평가 제한 | 131,072자 | `@concat()`, `@base64()`, `@string()` 식은 이 제한보다 길 수 없습니다. |
| 요청 URL 문자 제한 | 16,384자 | |
||||

<a name="retry-policy-limits"></a>

### <a name="retry-policy"></a>다시 시도 정책

| 속성 | 제한 | 메모 |
| ---- | ----- | ----- |
| 다시 시도 횟수 | - 기본값: 시도 4회 <br> - 최대: 시도 90회 | 기본값을 변경하려면 [정책 매개 변수 재시도](../logic-apps/logic-apps-workflow-actions-triggers.md)를 사용합니다. |
| 재시도 최대 지연 시간 | - 기본값: 1 일 | 기본값을 변경하려면 [정책 매개 변수 재시도](../logic-apps/logic-apps-workflow-actions-triggers.md)를 사용합니다. |
| 재시도 최소 지연 시간 | - 기본값: 5초 | 기본값을 변경하려면 [정책 매개 변수 재시도](../logic-apps/logic-apps-workflow-actions-triggers.md)를 사용합니다. |
||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>인증 제한

다음 표에는 요청 트리거로 시작하고 요청 트리거에 대한 인바운드 호출 권한을 부여하는 데 Azure AD OAuth([Azure Active Directory Open Authentication](../active-directory/develop/index.yml))를 사용하는 워크플로의 값이 나와 있습니다.

| 속성 | 제한 | 메모 |
| ---- | ----- | ----- |
| Azure AD 권한 부여 정책 | 정책 5개 | |
| 권한 부여 정책당 클레임 | 클레임 10개 | |
| 클레임 값 - 최대 문자 수 | 150자 |
||||

<a name="switch-action-limits"></a>

## <a name="switch-action-limits"></a>전환 작업 제한

다음 표에는 단일 워크플로 정의 값이 나와 있습니다.

| 속성 | 제한 | 메모 |
| ---- | ----- | ----- |
| 작업당 최대 사례 수 | 25 ||
||||

<a name="inline-code-action-limits"></a>

## <a name="inline-code-action-limits"></a>인라인 코드 작업 제한

다음 표에는 단일 워크플로 정의 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 최대 코드 문자 수 | 1,024자 | 100,000자 | 1,024자 | 더 높은 제한을 사용하려면 [Azure Portal을 사용하거나](create-single-tenant-workflows-azure-portal.md) [Visual Studio Code와 **Azure Logic Apps(표준)** 확장을 사용](create-single-tenant-workflows-visual-studio-code.md)하여 단일 테넌트 Azure Logic Apps에서 실행되는 **Logic Apps(표준)** 리소스를 만듭니다. |
| 최대 코드 실행 기간 | 5초 | 15초 | 1,024자 | 더 높은 제한을 사용하려면 [Azure Portal을 사용하거나](create-single-tenant-workflows-azure-portal.md) [Visual Studio Code와 **Azure Logic Apps(표준)** 확장을 사용](create-single-tenant-workflows-visual-studio-code.md)하여 단일 테넌트 Azure Logic Apps에서 실행되는 **Logic Apps(표준)** 리소스를 만듭니다. |
||||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>사용자 지정 커넥터 제한

다중 테넌트 및 통합 서비스 환경의 경우에만 기존 REST API 또는 SOAP API의 래퍼인 [사용자 지정 관리형 커넥터](/connectors/custom-connectors)를 만들어 사용할 수 있습니다. 단일 테넌트의 경우에만 [사용자 지정 기본 제공 커넥터](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 만들어 사용할 수 있습니다.

다음 표에는 사용자 지정 커넥터 값이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| 사용자 지정 커넥터 | Azure 구독당 1,000개 | 제한 없음 | Azure 구독당 1,000개 ||
| 사용자 지정 커넥터의 분당 요청 | 연결마다 분당 500개 요청 | 구현 기준 | *사용자 지정 커넥터* 마다 분당 2,000개 요청 ||
| 연결 제한 시간 | 2분 | 유휴 연결: <br>4분 <p><p>활성 연결: <br>10분 | 2분 ||
||||||

자세한 내용은 다음 설명서를 검토하세요.

* [사용자 지정 관리형 커넥터 개요](/connectors/custom-connectors)
* [기본 제공 커넥터 제작 사용 - Azure Logic Apps(표준) 확장과 Visual Studio Code 사용](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## <a name="managed-identity-limits"></a>관리 ID 제한

| 속성 | 제한 |
|------|-------|
| 논리 앱당 관리 ID | 시스템 할당 ID 또는 사용자 할당 ID 1개 |
| 지역별 Azure 구독에서 관리 ID에 포함되는 논리 앱 수 | 1,000 |
|||

> [!NOTE] 
> 기본적으로 논리 앱(표준) 리소스에는 런타임 시 연결을 인증하는 데 자동으로 사용되는 시스템이 할당한 관리 ID가 있습니다. 이 ID는 연결을 만들 때 사용하는 인증 자격 증명이나 연결 문자열과는 다릅니다. 이 ID를 사용하지 않도록 설정하면 런타임에 연결이 작동하지 않습니다. 이 설정을 보려면 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>통합 계정 제한

각 Azure 구독에는 다음과 같은 통합 계정 제한이 적용됩니다.

* Azure 지역당 [무료 계층](../logic-apps/logic-apps-pricing.md#integration-accounts) 통합 계정 하나. 이 계층은 Azure의 공용 지역(예: 미국 서부 또는 동남 아시아)에 대해서만 사용할 수 있고, [Azure 중국 21Vianet](/azure/china/overview-operations) 또는 [Azure Government](../azure-government/documentation-government-welcome.md)에는 사용할 수 없습니다.

* [개발자 및 프리미엄 SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)의 모든 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에 포함된 통합 계정을 포함하여 통합 계정 총 1,000개

* 포함된 계정 유형은 ISE SKU에 따라 다르지만 [개발자 또는 프리미엄](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 관계없이 각 ISE에서 추가 비용 없이 단일 통합 계정을 사용할 수 있습니다. [추가 비용](logic-apps-pricing.md#ise-pricing)의 총 한도까지 ISE의 통합 계정을 추가로 만들 수 있습니다.

  | ISE SKU | 통합 계정 제한 |
  |---------|----------------------------|
  | **Premium** | 계정 총 20개(추가 비용 없이 표준 계정 하나 포함). 이 SKU를 사용하는 경우 [표준](../logic-apps/logic-apps-pricing.md#integration-accounts) 계정만 사용할 수 있습니다. 체험 또는 기본 계정은 허용되지 않습니다. |
  | **개발자** | 1개의 [체험](../logic-apps/logic-apps-pricing.md#integration-accounts) 계정(1개로 제한됨)을 포함하여 총 20개의 계정. 이 SKU를 사용하는 경우 다음 두 가지 조합을 사용할 수 있습니다. <p>- 1개의 체험 계정과 최대 19개의 [표준](../logic-apps/logic-apps-pricing.md#integration-accounts) 계정. <br>- 체험 계정 없이 최대 20개의 표준 계정. <p>기본 또는 추가 체험 계정은 허용되지 않습니다. <p><p>**중요**: [개발자 SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)는 실험, 개발 및 테스트 용도에만 사용하고 프로덕션 또는 성능 테스트에는 사용하지 마세요. |
  |||

ISE의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#ise-pricing)을 참조하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요.

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>통합 계정당 아티팩트 제한

다음 표에는 각 통합 계정 계층으로 제한되는 아티팩트 수에 대한 값이 나와 있습니다. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. 통합 계정의 가격 책정 및 요금 청구 방식은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md#integration-accounts)을 참조하세요.

> [!NOTE]
> 체험 계층은 예비 시나리오에만 사용하고 프로덕션 시나리오에는 사용하지 마세요. 이 계층에서는 처리량과 사용량을 제한하며 SLA(서비스 수준 계약)가 없습니다.

| 아티팩트 | 무료 | Basic | Standard |
|----------|------|-------|----------|
| EDI 거래 계약 | 10 | 1 | 1,000 |
| EDI 거래 업체 | 25 | 2 | 1,000 |
| 지도 | 25 | 500 | 1,000 |
| 스키마 | 25 | 500 | 1,000 |
| 어셈블리 | 10 | 25 | 1,000 |
| 인증서 | 25 | 2 | 1,000 |
| 일괄 처리 구성 | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>아티팩트 용량 제한

| 아티팩트 | 제한 | 메모 |
| -------- | ----- | ----- |
| 어셈블리 | 8MB | 2MB보다 큰 파일을 업로드하려면 [Azure 스토리지 계정 및 Blob 컨테이너](../logic-apps/logic-apps-enterprise-integration-schemas.md)를 사용합니다. |
| 맵(XSLT 파일) | 8MB | 2MB보다 큰 파일을 업로드하려면 [Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate)를 사용합니다. <p><p>**참고**: 맵이 성공적으로 처리할 수 있는 데이터 또는 레코드의 양은 Azure Logic Apps의 메시지 크기와 작업 시간 제한에 따라 달라집니다. 예를 들어 [HTTP 메시지 크기 및 시간 제한](#http-limits)을 기준으로 HTTP 작업을 사용하는 경우 작업이 HTTP 시간 제한 내에 완료된다면 맵은 HTTP 메시지 크기 제한까지 데이터를 처리할 수 있습니다. |
| 스키마 | 8MB | 2MB보다 큰 파일을 업로드하려면 [Azure 스토리지 계정 및 Blob 컨테이너](../logic-apps/logic-apps-enterprise-integration-schemas.md)를 사용합니다. |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>처리량 한도

| 런타임 엔드포인트 | 무료 | Basic | Standard | 메모 |
|------------------|------|-------|----------|-------|
| 5분당 읽기 호출 | 3,000 | 30,000 | 60,000 | 이 제한은 논리 앱의 실행 기록에서 원시 입력 및 출력을 가져오는 호출에 적용됩니다. 필요에 따라 둘 이상의 계정에 워크로드를 배포할 수 있습니다. |
| 5분당 Invoke 호출 | 3,000 | 30,000 | 45,000 | 필요에 따라 둘 이상의 계정에 워크로드를 배포할 수 있습니다. |
| 5분당 추적 호출 | 3,000 | 30,000 | 45,000 | 필요에 따라 둘 이상의 계정에 워크로드를 배포할 수 있습니다. |
| 동시 차단 호출 | ~1,000 | ~1,000 | ~1,000 | 모든 SKU에 동일하게 적용됩니다. 필요에 따라 동시 요청 수를 줄이거나 기간을 단축할 수 있습니다. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B 프로토콜(AS2, X12, EDIFACT) 메시지 크기

다음 표에는 B2B 프로토콜에 적용되는 메시지 크기 제한이 나와 있습니다.

| 이름 | 다중 테넌트 | 단일 테넌트 | 통합 서비스 환경 | 메모 |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100MB<br>v1 - 25MB | Unavailable | v2 - 200MB <br>v1 - 25MB | 디코딩 및 인코딩에 적용됩니다. |
| X12 | 50MB | Unavailable | 50MB | 디코딩 및 인코딩에 적용됩니다. |
| EDIFACT | 50MB | Unavailable | 50MB | 디코딩 및 인코딩에 적용됩니다. |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>방화벽 구성: IP 주소 및 서비스 태그

환경에 엄격한 네트워크 요구 사항이나 트래픽을 특정 IP 주소로 제한하는 방화벽이 있는 경우 환경이나 방화벽에서 Azure Logic Apps 서비스에서 또는 논리 앱 리소스가 있는 Azure 지역의 런타임에서 사용하는 [인바운드](#inbound) 및 [아웃바운드](#outbound) IP 주소 *모두* 에 대한 액세스를 허용해야 합니다. 이 액세스를 설정하기 위해 [Azure Firewall 규칙](../firewall/rule-processing.md)을 만들 수 있습니다. 동일한 지역의 *모든* 논리 앱은 동일한 IP 주소 범위를 사용합니다.

> [!NOTE]
> [Power Automate](/power-automate/getting-started)를 사용하는 경우, **HTTP** 및 **HTTP + OpenAPI** 같은 일부 작업은 Azure Logic Apps 서비스를 직접 거쳐 여기에 나열된 IP 주소에서 나옵니다. Power Automate에서 사용하는 IP 주소에 대한 자세한 내용은 [Power Automate의 제한 및 구성](/flow/limits-and-config#ip-address-configuration)을 참조하세요.

예를 들어 논리 앱이 미국 서부 지역에 배포된다고 가정합니다. 논리 앱에서 [HTTP 트리거 또는 작업](../connectors/connectors-native-http.md)과 같은 기본 제공 트리거와 작업을 통해 보내거나 받는 호출을 지원하려면 방화벽에서 미국 서부 지역에 있는 *모든* Azure Logic Apps 서비스 인바운드 IP 주소 *및* 아웃바운드 IP 주소에 대한 액세스를 허용해야 합니다.

워크플로에서 Office 365 Outlook 커넥터나 SQL 커넥터와 같은 [관리형 커넥터](../connectors/managed.md)를 사용하거나 [사용자 지정 커넥터](/connectors/custom-connectors/)를 사용하는 경우 방화벽에서 논리 앱의 Azure 지역에 있는 ‘모든’ [관리형 커넥터 아웃바운드 IP 주소](/connectors/common/outbound-ip-addresses)에 대한 액세스도 허용해야 합니다. 워크플로가 [Azure의 온-프레미스 데이터 게이트웨이 리소스](logic-apps-gateway-connection.md)를 통해 온-프레미스 리소스에 액세스하는 사용자 지정 커넥터를 사용하는 경우 해당 [‘관리형 커넥터’ 아웃바운드 IP 주소](/connectors/common/outbound-ip-addresses)에 대한 액세스를 허용하도록 게이트웨이 설치를 설정해야 합니다. 게이트웨이에서 통신 설정을 설정하는 방법에 관한 자세한 내용은 다음 항목을 검토합니다.

* [온-프레미스 데이터 게이트웨이에 대한 통신 설정 조정](/data-integration/gateway/service-gateway-communication)
* [온-프레미스 데이터 게이트웨이에 대한 프록시 설정 구성](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> [21Vianet에서 운영되는 Microsoft Azure](/azure/china/)을 사용하는 경우 관리형 커넥터와 사용자 지정 커넥터에는 예약되거나 고정된 IP 주소가 없습니다. 따라서 이 클라우드에서 해당 커넥터를 사용하는 논리 앱에 대한 방화벽 규칙을 설정할 수 없습니다. Azure Logic Apps 서비스 IP의 경우 [21Vianet에서 운영되는 Azure의 설명서 버전](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration)을 검토합니다.

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>방화벽 IP 구성 고려 사항

IP 주소를 사용하여 방화벽을 설정하기 전에 다음 사항을 검토하세요.

* 논리 앱 워크플로가 단일 테넌트 Azure Logic Apps에서 실행되는 경우 커넥터의 FQDN(정규화된 도메인 이름)을 찾아야 합니다. 자세한 내용은 다음 항목에서 해당 섹션을 검토합니다.

  * [단일 테넌트 논리 앱에 대한 방화벽 권한 - Azure Portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [단일 테넌트 논리 앱에 대한 방화벽 권한 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* 논리 앱 워크플로가 [ISE(통합 서비스 환경)](connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행되는 경우 [해당 포트도 열려 있는지](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise) 확인합니다.

* 만들려는 보안 규칙을 단순화하기 위해 각 지역의 IP 주소 접두사를 지정하는 대신 선택적으로 [서비스 태그](../virtual-network/service-tags-overview.md)를 사용할 수 있습니다. 이러한 태그는 Logic Apps 서비스가 제공되는 지역에서 작동합니다.

  * **LogicAppsManagement**: Logic Apps 서비스의 인바운드 IP 주소 접두사를 나타냅니다.

  * **LogicApps**: Logic Apps 서비스의 아웃바운드 IP 주소 접두사를 나타냅니다.

  * **AzureConnectors**: Logic Apps 서비스에 대한 인바운드 웹후크 콜백과 Azure Storage 또는 Azure Event Hubs와 같은 해당 서비스에 대한 아웃바운드 호출을 수행하는 관리되는 커넥터의 IP 주소 접두사를 나타냅니다.

* 논리 앱이 [방화벽 및 방화벽 규칙](../storage/common/storage-network-security.md)을 사용하는 Azure 스토리지 계정에 액세스하는 데 문제가 있는 경우 [액세스를 사용하도록 설정하는 다양한 기타 옵션](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)이 있습니다.

  예를 들어 논리 앱은 방화벽 규칙을 사용하고 동일한 지역에 있는 스토리지 계정에 직접 액세스할 수 없습니다. 그러나 [해당 지역의 관리형 커넥터에 대한 아웃바운드 IP 주소](../logic-apps/logic-apps-limits-and-config.md#outbound)를 허용하면 Azure Table Storage 또는 Azure Queue Storage 커넥터를 사용하는 경우를 제외하고 논리 앱이 다른 지역에 있는 스토리지 계정에 액세스할 수 있습니다. Table Storage 또는 Queue Storage에 액세스하려면 HTTP 트리거와 작업을 대신 사용하면 됩니다. 다른 옵션은 [방화벽으로 보호되는 스토리지 계정 액세스](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)를 참조하세요.

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>인바운드 IP 주소

이 섹션에서는 Azure Logic Apps 서비스의 인바운드 IP 주소만 나열합니다. Azure Government를 사용하는 경우 [Azure Government - 인바운드 IP 주소](#azure-government-inbound)를 참조하세요.

> [!TIP]
> 보안 규칙을 만들 때 지역마다 인바운드 Logic Apps IP 주소 접두사를 지정하는 대신 [서비스 태그](../virtual-network/service-tags-overview.md) **LogicAppsManagement** 를 사용하면 복잡성을 줄일 수 있습니다.
>
> 일부 관리형 커넥터는 Azure Logic Apps 서비스에 대한 인바운드 웹후크 콜백을 수행합니다. 이 관리형 커넥터의 경우 필요에 따라 각 지역에서 인바운드 관리형 커넥터 IP 주소 접두사를 지정하는 대신 이 관리형 커넥터에 대해 **AzureConnectors** 서비스 태그를 사용할 수 있습니다. 
> 이러한 태그는 Logic Apps 서비스가 제공되는 지역에서 작동합니다.
>
> 다음 커넥터는 Logic Apps 서비스에 대한 인바운드 웹후크 콜백을 수행합니다.
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \* **SAP**: 반환 호출자는 배포 환경이 다중 테넌트 Azure인지 ISE인지에 따라 다릅니다. 다중 테넌트 환경에서 온-프레미스 데이터 게이트웨이는 Logic Apps 서비스로 다시 호출합니다. ISE에서 SAP 커넥터는 Logic Apps 서비스로 다시 호출합니다.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant--single-tenant---inbound-ip-addresses"></a>다중 테넌트 및 단일 테넌트 - 인바운드 IP 주소

| 지역 | IP |
|--------|----|
| 오스트레일리아 동부 | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| 오스트레일리아 남동부 | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| 브라질 남부 | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| 브라질 남동부 | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| 캐나다 중부 | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| 캐나다 동부 | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| 인도 중부 | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| 미국 중부 | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| 동아시아 | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| 미국 동부 | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| 미국 동부 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| 프랑스 중부 | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| 프랑스 남부 | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| 독일 북부 | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| 독일 중서부 | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| 일본 동부 | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| 일본 서부 | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| 한국 중부 | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| 한국 남부 | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| 미국 중북부 | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| 북유럽 | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| 노르웨이 동부 | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| 남아프리카 북부 | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| 남아프리카 공화국 서부 | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| 미국 중남부 | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| 인도 남부 | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| 동남아시아 | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| 스위스 북부 | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| 스위스 서부 | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| 아랍에미리트 중부 | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| 아랍에미리트 북부 | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| 영국 남부 | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| 영국 서부 | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| 미국 중서부 | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| 서유럽 | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| 인도 서부 | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| 미국 서부 | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| 미국 서부 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government - 인바운드 IP 주소

| Azure Government 지역 | IP |
|-------------------------|----|
| US Gov 애리조나 | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov 텍사스 | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov 버지니아 | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD 중부 | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>아웃바운드 IP 주소

이 섹션에서는 Azure Logic Apps 서비스의 아웃바운드 IP 주소를 나열합니다. Azure Government를 사용하는 경우 [Azure Government - 아웃바운드 IP 주소](#azure-government-outbound)를 참조하세요.

> [!TIP]
> 보안 규칙을 만들 때 지역마다 아웃바운드 Logic Apps IP 주소 접두사를 지정하는 대신 [서비스 태그](../virtual-network/service-tags-overview.md) **LogicApps** 를 사용하면 복잡성을 줄일 수 있습니다. 필요에 따라 각 지역에 아웃바운드 관리되는 커넥터 IP 주소 접두사를 지정하는 대신 Azure Storage 또는 Azure Event Hubs와 같은 해당 서비스에 대한 아웃바운드 호출을 수행하는 관리되는 커넥터에 대해 **AzureConnectors** 서비스 태그를 사용할 수도 있습니다. 이러한 태그는 Logic Apps 서비스가 제공되는 지역에서 작동합니다.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant--single-tenant---outbound-ip-addresses"></a>다중 테넌트 및 단일 테넌트 - 아웃바운드 IP 주소

| 지역 | Logic Apps IP |
|--------|---------------|
| 오스트레일리아 동부 | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 |
| 오스트레일리아 남동부 | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 |
| 브라질 남부 | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 |
| 브라질 남동부 | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 |
| 캐나다 중부 | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 |
| 캐나다 동부 | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 |
| 인도 중부 | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 |
| 미국 중부 | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 |
| 동아시아 | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 |
| 미국 동부 | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 |
| 미국 동부 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 |
| 프랑스 중부 | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 |
| 프랑스 남부 | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 |
| 독일 북부 | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 |
| 독일 중서부 | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 |
| 일본 동부 | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 |
| 일본 서부 | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 |
| 한국 중부 | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 |
| 한국 남부 | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 |
| 미국 중북부 | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 |
| 북유럽 | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 |
| 노르웨이 동부 | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 |
| 남아프리카 북부 | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 |
| 남아프리카 공화국 서부 | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 |
| 미국 중남부 | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 |
| 인도 남부 | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 |
| 동남아시아 | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 |
| 스위스 북부 | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 |
| 스위스 서부 | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 |
| 아랍에미리트 중부 | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 |
| 아랍에미리트 북부 | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 |
| 영국 남부 | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 |
| 영국 서부 | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 |
| 미국 중서부 | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 |
| 서유럽 | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 |
| 인도 서부 | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 |
| 미국 서부 | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 |
| 미국 서부 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 |
|||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government - 아웃바운드 IP 주소

| 지역 | Logic Apps IP |
|--------|---------------|
| US DoD 중부 | 52.182.48.215, 52.182.92.143 |
| US Gov 애리조나 | 52.244.67.143, 52.244.65.66, 52.244.65.190 |
| US Gov 텍사스 | 52.238.114.217, 52.238.115.245, 52.238.117.119 |
| US Gov 버지니아 | 13.72.54.205, 52.227.138.30, 52.227.152.44 |
|||

## <a name="next-steps"></a>다음 단계

* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 대해 알아봅니다.
* [일반적인 예제 및 시나리오](../logic-apps/logic-apps-examples-and-scenarios.md)에 대해 알아봅니다.
