---
title: 사용량 계량, 청구 및 가격 책정
description: Azure Logic Apps에서 사용량 계량, 청구 및 가격 책정 모델이 작동하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/09/2021
ms.openlocfilehash: 63c7a2c79ca5f0d241ddc3727d006bb2befb8163
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537306"
---
# <a name="usage-metering-billing-and-pricing-models-for-azure-logic-apps"></a>Azure Logic Apps의 사용량 계량, 청구 및 가격 책정 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하면 클라우드에서 확장 가능한 자동화된 통합 워크플로를 만들어 실행할 수 있습니다. 이 문서에서는 Azure Logic Apps 및 관련 리소스에 대해 계량, 청구 및 가격 책정 모델이 작동하는 방법을 알아봅니다. 특정 가격 책정 요금, 비용 계획 또는 다양한 호스팅 환경과 같은 정보는 다음 콘텐츠를 검토하세요.

* [Azure Logic Apps 가격 책정 요금](https://azure.microsoft.com/pricing/details/logic-apps)
* [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)
* [단일 테넌트 대 다중 테넌트 및 통합 서비스 환경](single-tenant-overview-compare.md)

<a name="consumption-pricing"></a>

## <a name="consumption-multi-tenant"></a>사용량(다중 테넌트)

다중 테넌트 Azure Logic Apps에서 논리 앱과 해당 워크플로는 가격 책정 및 청구에 대한 [**사용량** 요금제](https://azure.microsoft.com/pricing/details/logic-apps)를 따릅니다. 예를 들어 **논리 앱(사용량)** 리소스 종류를 선택하거나 Visual Studio Code에서 **Azure Logic Apps(사용량)** 확장을 사용하거나 [자동화 작업](create-automation-tasks-azure-resources.md)을 만들 때 이러한 논리 앱을 다양한 방법으로 만듭니다.

다음 표에는 다중 테넌트 Azure Logic Apps에서 논리 앱 및 워크플로와 함께 사용하는 경우 사용량 모델에서 다음 구성 요소에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 구성 요소 | 계량 및 요금 청구 |
| ----------|----------------------|
| 트리거 및 작업 | 사용량 모델에는 워크플로에서 실행할 수 있는 Azure 구독당 무료 기본 제공 작업의 *초기 수* 가 포함됩니다. 이 수를 초과하는 경우 계량이 *각 실행* 에 적용되며, [사용량 요금제의 *작업* 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 따릅니다. 관리형 커넥터와 같은 다른 작업 유형의 경우 청구는 [사용량 요금제의 *표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 따릅니다. 자세한 내용은 [사용량 모델의 트리거 및 작업](#consumption-operations)을 검토하세요. |
| 스토리지 작업 | 계량은 워크플로의 실행 기록에서 입력 및 출력을 저장하는 것과 같은 *데이터 보존 관련 스토리지 사용량* 에만 적용됩니다. 청구는 [사용량 요금제의 데이터 보존 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. 자세한 내용은 [스토리지 작업](#storage-operations)을 검토하세요. |
| 통합 계정 | 계량은 논리 앱에서 만들고 사용하는 통합 계정 유형에 따라 적용됩니다. 청구는 논리 앱이 [ISE(통합 서비스 환경)](#ise-pricing)에서 배포되고 호스트되는 경우를 제외하고 [*통합 계정* 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. 자세한 내용은 [통합 계정](#integration-accounts)을 검토하세요. |
|||

<a name="consumption-operations"></a>

### <a name="trigger-and-action-operations-in-the-consumption-model"></a>사용량 모델의 트리거 및 작업

전체 워크플로가 성공적으로 실행, 완료, 심지어 인스턴스화되었는지의 여부에 관계없이 워크플로에서 실행할 수 있는 Azure 구독당 무료 기본 제공 작업 실행의 초기 수를 제외하고는 사용량 모델에서 *각 실행* 을 기반으로 하여 작업을 계량하고 요금을 청구합니다. 작업은 [재시도 횟수가 사용하도록 설정된 경우를 제외](#other-operation-behavior)하고는 일반적으로 단일 실행을 수행합니다. 결과적으로 [작업에서 청크 또는 페이지 매김을 지원하고 사용하도록 설정하여 대량의 데이터를 가져오는 경우를 제외](logic-apps-handle-large-messages.md)하고는 실행에서 일반적으로 단일 호출을 수행합니다. 청크 또는 페이지 매김이 사용하도록 설정된 경우 작업 실행에서 여러 호출을 번 수행해야 할 수 있습니다. 사용량 모델은 작업을 *호출 단위가 아니라 실행 단위로* 계량하고 청구합니다.

예를 들어 워크플로에서 엔드포인트에 대한 아웃바운드 호출을 정기적으로 수행하여 레코드를 가져오는 폴링 트리거로 시작한다고 가정합니다. 아웃바운드 호출은 트리거가 발생하거나 건너뛰는지 여부에 관계없이 단일 실행으로 계량되고 청구됩니다. 트리거 상태는 워크플로 인스턴스를 만들고 실행할지 여부를 제어합니다. 이제 작업에서 청크 또는 페이지 매김도 지원하고 사용하도록 설정했다고 가정합니다. 작업에서 모든 데이터 가져오기를 완료하기 위해 10번의 호출을 수행해야 하는 경우 여러 번 호출하더라도 작업은 여전히 *단일 실행* 으로 계량되고 청구됩니다.

다음 표에는 다중 테넌트 Azure Logic Apps에서 논리 앱 및 워크플로와 함께 사용하는 경우 사용량 모델에서 이러한 작업 유형에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 작업 유형 | 설명 | 계량 및 요금 청구 |
|----------------|-------------|----------------------|
| [*기본 제공*](../connectors/built-in.md) | 이러한 작업은 Azure Logic Apps 런타임에서 기본적으로 직접 실행됩니다. 이러한 작업은 디자이너의 **기본 제공** 레이블에서 찾을 수 있습니다. <p>예를 들어 HTTP 트리거 및 요청 트리거는 기본 제공 트리거입니다. HTTP 작업 및 응답 작업은 기본 제공 작업입니다. 기타 기본 제공 작업에는 루프 및 조건, 데이터 작업, 일괄 처리 작업 등과 같은 워크플로 제어 작업이 포함됩니다. | 사용량 모델에는 워크플로에서 실행할 수 있는 Azure 구독당 *무료 기본 제공 작업의 초기 수* 가 포함됩니다. 이 수를 초과하는 경우 기본 제공 작업 실행은 [*작업* 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. <p><p>**참고**: 일부 관리형 커넥터 *작업도* 초기 무료 작업에 포함된 기본 제공 작업으로 사용할 수 있습니다. 초기 무료 작업 수를 초과하는 경우 청구는 [*표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)이 아닌 [*작업* 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. |
| [*관리형 커넥터*](../connectors/managed.md) | 이러한 작업은 Azure에서 별도로 실행됩니다. 이러한 작업은 디자이너의 **표준** 또는 **엔터프라이즈** 레이블에서 찾을 수 있습니다. | 이러한 작업 실행은 [*표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. <p><p>**참고**: 미리 보기 엔터프라이즈 커넥터 작업 실행은 [사용량 *표준* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. |
| [*사용자 지정 커넥터*](../connectors/apis-list.md#custom-apis-and-connectors) | 이러한 작업은 Azure에서 별도로 실행됩니다. 이러한 작업은 디자이너의 **사용자 지정** 레이블에서 찾을 수 있습니다. 커넥터 수, 처리량 및 시간 제한에 대한 제한은 [Azure Logic Apps의 사용자 지정 커넥터 제한](logic-apps-limits-and-config.md#custom-connector-limits)을 검토하세요. | 이러한 작업 실행은 [*표준* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. |
||||

사용량 모델이 루프와 같은 다른 작업 내에서 실행하고, 배열과 같은 여러 항목을 처리하고, 정책을 다시 시도하는 작업을 사용하는 방법에 대한 자세한 내용은 [기타 작업 동작](#other-operation-behavior)을 검토하세요.

<a name="consumption-cost-estimation-tips"></a>

### <a name="cost-estimation-tips-for-the-consumption-model"></a>사용량 모델에 대한 비용 예측 팁

더 정확한 소비 비용 예측을 위해 다음 팁을 검토하세요.

* 폴링 간격만을 기반으로 계산하기 보다는 지정된 날짜에 수신할 수 있는 메시지 또는 이벤트 수를 고려합니다.

* 이벤트나 메시지가 트리거 조건을 충족하면 많은 트리거가 조건을 충족하는 대기 중인 다른 이벤트나 메시지를 즉시 읽으려고 시도합니다. 이러한 동작은 폴링 간격을 길게 선택하더라도 워크플로를 시작하기에 적합한 대기 중인 이벤트나 메시지 수를 기반으로 트리거가 실행된다는 것을 의미합니다. 이러한 동작을 따르는 트리거에는 Azure Service Bus 및 Azure Event Hub가 있습니다.

  예를 들어, 매일 엔드포인트를 확인하는 트리거를 설정한다고 가정하겠습니다. 트리거가 엔드포인트를 확인하여 조건을 충족하는 이벤트를 15개 찾으면, 트리거가 실행되고 해당 워크플로가 15번 실행됩니다. Logic Apps 서비스는 트리거 요청을 포함하여 15개 워크플로에서 수행하는 모든 작업을 측정합니다.

<a name="standard-pricing"></a>

## <a name="standard-single-tenant"></a>표준(단일 테넌트)

단일 테넌트 Azure Logic Apps에서 논리 앱과 해당 워크플로는 가격 책정 및 청구에 대한 [**표준** 요금제](https://azure.microsoft.com/pricing/details/logic-apps/)를 따릅니다. 예를 들어 **논리 앱(표준)** 리소스 종류를 선택하거나 Visual Studio Code에서 **Azure Logic Apps(표준)** 확장을 사용할 때 이러한 논리 앱을 다양한 방법으로 만들 수 있습니다. 이 가격 책정 모델에서는 논리 앱에서 호스팅 계획 및 가격 책정 계층을 사용해야 합니다. 이는 사용하는지 여부에 관계없이 요금이 예약된 용량 및 전용 리소스에 대해 청구된다는 점에서 사용량 요금제와 다릅니다.

> [!IMPORTANT]
> **논리 앱(표준)** 리소스 종류를 기반으로 하여 새 논리 앱을 만들거나 배포하는 경우 모든 Azure 지역에서 워크플로 표준 호스팅 계획을 사용하거나 App Service 호스팅 계획을 사용할 수 있지만, **기본 사항** 탭에서 **App Service Environment v3** 지역을 선택하는 경우에만 사용할 수 있습니다.
>
> 미리 보기 **논리 앱(표준)** 리소스 종류를 사용하면 App Service 요금제, Functions 프리미엄 요금제, App Service Environment v1 및 App Service Environment v2를 사용할 수 있지만, 이러한 옵션은 이 Azure Logic Apps 리소스 종류의 퍼블릭 릴리스에서 더 이상 사용할 수 없거나 지원되지 않습니다.

다음 표에는 단일 테넌트 Azure Logic Apps에서 논리 앱 및 워크플로와 함께 사용하는 경우 표준 모델에서 다음 구성 요소에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 구성 요소 | 계량 및 요금 청구 |
|-----------|----------------------|
| vCPU(가상 CPU) 및 메모리 | 표준 모델을 사용하려면 논리 앱에서 **워크플로 표준** 호스팅 계획과 컴퓨팅 및 메모리 용량에 적용되는 리소스 수준 및 가격 책정 요금을 결정하는 가격 책정 계층을 *사용해야* 합니다. 자세한 내용은 [표준 모델의 가격 책정 계층](#standard-pricing-tiers)을 참조하세요. |
| 트리거 및 작업 | 표준 모델에는 워크플로에서 실행할 수 있는 *무제한* 의 무료 기본 제공 작업이 포함됩니다. <p>워크플로에서 관리형 커넥터 작업을 사용하는 경우 해당 작업에 대한 계량은 *각 호출* 에 적용되며, 청구는 [사용량 요금제와 동일한 *표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 따릅니다. 자세한 내용은 [표준 모델의 트리거 및 작업](#standard-operations)을 검토하세요. |
| 스토리지 작업 | 계량은 Azure Logic Apps에서 실행하는 모든 스토리지 작업에 적용됩니다. 예를 들어 스토리지 작업은 서비스에서 워크플로 실행 기록의 입력 및 출력을 저장할 때 실행됩니다. 청구는 선택한 [가격 책정 계층](#standard-pricing-tiers)을 따릅니다. 자세한 내용은 [스토리지 작업](#storage-operations)을 검토하세요. |
| 통합 계정 | 논리 앱에서 사용할 통합 계정을 만드는 경우 계량은 사용자가 만드는 통합 계정 유형을 기반으로 합니다. 청구는 선택한 [*통합 계정* 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. 자세한 내용은 [통합 계정](#integration-accounts)을 검토하세요. |
|||

<a name="standard-pricing-tiers"></a>

### <a name="pricing-tiers-in-the-standard-model"></a>표준 모델의 가격 책정 계층

논리 앱을 계량하고 청구하기 위해 선택하는 가격 책정 계층에는 vCPU(가상 CPU) 및 메모리 리소스의 특정 컴퓨팅 양이 포함됩니다. 현재 **논리 앱(표준)** 리소스 종류에는 **워크플로 표준** 호스팅 계획만 사용할 수 있으며, 다음과 같은 가격 책정 계층을 제공합니다.

| 가격 책정 계층 | vCPU(가상 CPU) | 메모리(GB) |
|--------------|--------------------|-------------|
| **WS1** | 1 | 3.5 |
| **WS2** | 2 | 7 |
| **WS3** | 4 | 14 |
||||

> [!IMPORTANT]
>
> 다음 예는 설명을 위한 것이며, 일반적으로 가격 책정 계층의 작동 방식을 보여 주는 샘플 예상 비용을 제공합니다. 
> Azure Logic Apps를 사용할 수 있는 특정 지역을 기반으로 하는 특정 vCPU 및 메모리 가격 책정은 [Azure Logic Apps 가격 페이지에서 선택한 지역에 대한 표준 요금제](https://azure.microsoft.com/pricing/details/logic-apps/)를 검토하세요.
>
> 예제 지역에서 이러한 리소스의 시간당 요금이 다음과 같다고 가정합니다.
>
> | 리소스 | 시간당 요금(예제 지역) |
> |----------|-----------------------------|
> | **vCPU** | vCPU당 $0.192 |
> | **메모리** | GB당 $0.0137 |
> |||
>
> 다음 계산은 월별 예상 요금을 제공합니다.
>
> <*월간 요금*> = 730시간(월별) * [(<*number-vCPU*> * <*hourly-rate-vCPU*>) + (<*number-GB-memory*> * <*hourly-rate-GB-memory*>)]
>
> 다음 표에는 위의 정보를 기반으로 하여 각 가격 책정 계층 및 해당 가격 책정 계층의 리소스에 대한 월별 예상 요금이 나와 있습니다.
>
> | 가격 책정 계층 | vCPU(가상 CPU) | 메모리(GB) | 월별 요금(예제 지역) |
> |--------------|--------------------|-------------|------------------------------|
> | **WS1** | 1 | 3.5 | $175.16 |
> | **WS2** | 2 | 7 | $350.33 |
> | **WS3** | 4 | 14 | $700.65 |
> |||||

<a name="standard-operations"></a>

### <a name="trigger-and-action-operations-in-the-standard-model"></a>표준 모델의 트리거 및 작업

전체 워크플로가 성공적으로 실행, 완료, 심지어 인스턴스화되었는지의 여부에 관계없이 워크플로에서 실행할 수 있는 무제한의 무료 기본 제공 작업을 제외하고는 표준 모델에서 *각 호출* 을 기반으로 하여 작업을 계량하고 요금을 청구합니다. 작업은 [재시도 횟수가 사용하도록 설정된 경우를 제외](#other-operation-behavior)하고는 일반적으로 단일 실행을 수행합니다. 결과적으로 [작업에서 청크 또는 페이지 매김을 지원하고 사용하도록 설정하여 대량의 데이터를 가져오는 경우를 제외](logic-apps-handle-large-messages.md)하고는 실행에서 일반적으로 단일 호출을 수행합니다. 청크 또는 페이지 매김이 사용하도록 설정된 경우 작업 실행에서 여러 호출을 번 수행해야 할 수 있습니다. 표준 모델은 작업을 *실행 단위가 아니라 호출 단위로* 계량하고 청구합니다.

예를 들어 워크플로에서 엔드포인트에 대한 아웃바운드 호출을 정기적으로 수행하여 레코드를 가져오는 폴링 트리거로 시작한다고 가정합니다. 아웃바운드 호출은 트리거가 발생하거나 건너뛰는지 여부에 관계없이 계량되고 청구됩니다. 트리거 상태는 워크플로 인스턴스를 만들고 실행할지 여부를 제어합니다. 이제 작업에서 청크 또는 페이지 매김도 지원하고 사용하도록 설정했다고 가정합니다. 작업에서 모든 데이터 가져오기를 완료하기 위해 10번의 호출을 수행해야 하는 경우 작업은 *호출 단위* 로 계량되고 청구됩니다.

다음 표에는 단일 테넌트 Azure Logic Apps에서 논리 앱 및 워크플로와 함께 사용하는 경우 표준 모델에서 작업 유형에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 작업 유형 | 설명 | 계량 및 요금 청구 |
|----------------|-------------|----------------------|
| [*기본 제공*](../connectors/built-in.md) | 이러한 작업은 Azure Logic Apps 런타임에서 기본적으로 직접 실행됩니다. 이러한 작업은 디자이너의 **기본 제공** 레이블에서 찾을 수 있습니다. <p>예를 들어 HTTP 트리거 및 요청 트리거는 기본 제공 트리거입니다. HTTP 작업 및 응답 작업은 기본 제공 작업입니다. 기타 기본 제공 작업에는 루프 및 조건, 데이터 작업, 일괄 처리 작업 등과 같은 워크플로 제어 작업이 포함됩니다. | 표준 모델에는 무제한의 무료 기본 제공 작업이 포함됩니다. <p><p>**참고**: 일부 관리형 커넥터 *작업도* 기본 제공 작업으로 사용할 수 있습니다. 기본 제공 작업은 무료이지만, 표준 모델은 여전히 [사용량 모델과 동일한 *표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용하여 관리형 커넥터 작업을 계량하고 청구합니다. |
| [*관리형 커넥터*](../connectors/managed.md) | 이러한 작업은 Azure에서 별도로 실행됩니다. 이러한 작업은 디자이너의 결합된 **Azure** 레이블에서 찾을 수 있습니다. | 표준 모델은 [사용량 모델과 동일한 *표준* 또는 *엔터프라이즈* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 기반으로 하여 관리형 커넥터 작업을 계량하고 청구합니다. <p><p>**참고**: 미리 보기 엔터프라이즈 커넥터 작업은 [사용량 *표준* 커넥터 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 따릅니다. |
| [*사용자 지정 커넥터*](../connectors/apis-list.md#custom-apis-and-connectors) | 현재 단일 테넌트 기반 논리 앱 워크플로에서 [사용자 지정 기본 제공 커넥터 작업](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)만 만들고 사용할 수 있습니다. | 표준 모델에는 무제한의 무료 기본 제공 작업이 포함됩니다. 처리량 및 시간 제한에 대한 제한은 [Azure Logic Apps의 사용자 지정 커넥터 제한](logic-apps-limits-and-config.md#custom-connector-limits)을 검토하세요. |
||||

표준 모델이 루프와 같은 다른 작업 내에서 실행하고, 배열과 같은 여러 항목을 처리하고, 정책을 다시 시도하는 작업을 사용하는 방법에 대한 자세한 내용은 [기타 작업 동작](#other-operation-behavior)을 검토하세요.

<a name="ise-pricing"></a>

## <a name="integration-service-environment-ise"></a>ISE(통합 서비스 환경)

**논리 앱(사용량)** 리소스 종류를 사용하여 논리 앱을 만들고 전용 [*ISE*(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에 배포하는 경우 논리 앱과 해당 워크플로는 가격 책정 및 청구에 대한 [통합 서비스 환경 요금제](https://azure.microsoft.com/pricing/details/logic-apps)를 따릅니다. 이 가격 책정 모델은 [ISE 수준 또는 *SKU*](connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 따라 달라지며, 사용하는지 여부에 관계없이 요금이 예약된 용량 및 전용 리소스에 대해 청구된다는 점에서 사용량 요금제와 다릅니다.

다음 표에는 ISE 모델에서 ISE 수준 또는 SKU에 따라 용량 및 기타 전용 리소스에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| ISE SKU | 계량 및 요금 청구 |
|---------|----------------------|
| **Premium** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [프리미엄 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 더 많은 처리량이 필요한 경우 ISE를 만들 때 또는 나중에 [배율 단위를 더 추가](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)할 수 있습니다. 각 배율 단위는 [기준 단위 요금의 약 절반에 해당하는 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
| **개발자** | 기준 단위는 [고정 용량](logic-apps-limits-and-config.md#integration-service-environment-ise)이며, [개발자 SKU에 대한 시간당 요금](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구됩니다. 그러나 이 SKU는 SLA(서비스 수준 계약), 스케일 업 기능 또는 재활용 시 중복성이 없으므로, 지연 또는 가동 중지 시간이 발생할 수 있습니다. 백 엔드 업데이트로 인해 서비스가 간헐적으로 중단될 수 있습니다. <p><p>**중요**: 이 SKU는 프로덕션 또는 성능 테스트가 아닌 탐색, 실험, 개발 및 테스트에만 사용해야 합니다. <p><p>용량 및 한도 정보는 [Azure Logic Apps의 ISE 한도](logic-apps-limits-and-config.md#integration-service-environment-ise)를 참조하세요. |
|||

다음 표에는 ISE에서 논리 앱 및 워크플로와 함께 사용하는 경우 ISE 모델에서 다음 구성 요소를 처리하는 방법이 요약되어 있습니다.

| 구성 요소 | 설명 |
|-----------|-------------|
| 트리거 및 작업 | ISE 모델에는 워크플로에서 실행할 수 있는 무료 기본 제공 관리형 커넥터 및 사용자 지정 커넥터 작업이 포함되지만, [Azure Logic Apps의 ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise) 및 [Azure Logic Apps의 사용자 지정 커넥터 제한](logic-apps-limits-and-config.md#custom-connector-limits)이 적용됩니다. 자세한 내용은 [ISE 모델의 트리거 및 작업](#integration-service-environment-operations)을 검토하세요. |
| 스토리지 작업 | ISE 모델에는 데이터 보존과 같은 무료 스토리지 사용량이 포함됩니다. 자세한 내용은 [스토리지 작업](#storage-operations)을 검토하세요. |
| 통합 계정 | ISE 모델에는 선택한 ISE SKU에 따라 하나의 무료 통합 계정 계층이 포함됩니다. [추가 비용](https://azure.microsoft.com/pricing/details/logic-apps/)을 지불하면 ISE에서 [총 ISE 제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)까지 사용할 더 많은 통합 계정을 만들 수 있습니다. 자세한 내용은 [통합 계정](#integration-accounts)을 검토하세요. |
|||

<a name="integration-service-environment-operations"></a>

### <a name="trigger-and-action-operations-in-the-ise-model"></a>ISE 모델의 트리거 및 작업

다음 표에는 ISE에서 논리 앱 및 워크플로와 함께 사용하는 경우 ISE 모델에서 다음 작업 유형을 처리하는 방법이 요약되어 있습니다.

| 작업 유형 | 설명 | 계량 및 요금 청구 |
|----------------|-------------|----------------------|
| [*기본 제공*](../connectors/built-in.md) | 이러한 작업은 Azure Logic Apps 런타임 및 논리 앱 워크플로와 동일한 ISE에서 기본적으로 직접 실행됩니다. 이러한 작업은 디자이너의 **기본 제공** 레이블에서 찾을 수 있지만, 각 작업에는 **CORE** 레이블도 표시됩니다. <p>예를 들어 HTTP 트리거 및 요청 트리거는 기본 제공 트리거입니다. HTTP 작업 및 응답 작업은 기본 제공 작업입니다. 기타 기본 제공 작업에는 루프 및 조건, 데이터 작업, 일괄 처리 작업 등과 같은 워크플로 제어 작업이 포함됩니다. | ISE 모델에는 이러한 작업이 *무료로* 포함되지만, [Azure Logic Apps의 ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise)이 적용됩니다. |
| [*관리형 커넥터*](../connectors/managed.md) | *표준* 또는 *엔터프라이즈* 인지의 여부에 관계없이 커넥터 또는 작업에서 **ISE** 레이블을 표시하는지 여부에 따라 관리형 커넥터 작업은 ISE 또는 다중 테넌트 Azure에서 실행됩니다. <p><p>- **ISE** 레이블: 이러한 작업은 논리 앱과 동일한 ISE에서 실행되며 [온-프레미스 데이터 게이트웨이](#data-gateway) 없이 작동합니다. <p><p>- **ISE** 없음 레이블: 이러한 작업은 다중 테넌트 Azure에서 실행됩니다. | ISE 모델에는 **ISE** 및 **ISE** 없음 레이블이 지정된 작업이 모두 *무료로* 포함되지만, [Azure Logic Apps의 ISE 제한](logic-apps-limits-and-config.md#integration-service-environment-ise)이 적용됩니다. |
| [*사용자 지정 커넥터*](../connectors/apis-list.md#custom-apis-and-connectors) | 이러한 작업은 디자이너의 **사용자 지정** 레이블에서 찾을 수 있습니다. | ISE 모델에는 이러한 작업이 *무료로* 포함되지만, [Azure Logic Apps의 사용자 지정 커넥터 제한](logic-apps-limits-and-config.md#custom-connector-limits)이 적용됩니다. |
||||

ISE 모델이 루프와 같은 다른 작업 내에서 실행하고, 배열과 같은 여러 항목을 처리하고, 정책을 다시 시도하는 작업을 사용하는 방법에 대한 자세한 내용은 [기타 작업 동작](#other-operation-behavior)을 검토하세요.

<a name="other-operation-behavior"></a>

## <a name="other-operation-behavior"></a>기타 작업 동작

다음 표에는 사용량, 표준 및 ISE 모델에서 루프와 같은 다른 작업 내에서 실행하고, 배열과 같은 여러 항목을 처리하고, 정책을 다시 시도하는 작업을 처리하는 방법이 요약되어 있습니다.

| 작업(Operation) | Description | Consumption | 표준 | ISE |
|-----------|-------------|-------------|----------|-----|
| [루프 작업](logic-apps-control-flow-loops.md) | **For each** 또는 **Until** 루프와 같은 루프 작업에는 각 루프 주기 동안 실행되는 다른 작업이 포함될 수 있습니다. | 포함된 기본 제공 작업의 초기 수를 제외하고는 루프 주기가 실행될 때마다 루프 작업 및 루프의 각 작업이 계량됩니다. 작업에서 컬렉션의 항목(예: 목록 또는 배열)을 처리하는 경우 항목 수도 계량 계산에 사용됩니다. <p><p>예를 들어 목록을 처리하는 작업이 있는 **For each** 루프가 있다고 가정합니다. 서비스는 루프의 작업 수와 목록 항목 수를 곱하고, 루프를 시작하는 작업을 추가합니다. 따라서 10개 항목 목록에 대한 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다. <p><p>가격 책정은 작업 유형이 기본 제공, 표준 또는 엔터프라이즈인지 여부를 기반으로 합니다. | 포함된 기본 제공 작업을 제외하고는 사용량 모델과 동일합니다. | 계량되거나 청구되지 않습니다. |
| [재시도 정책](logic-apps-exception-handling.md#retry-policies) | 지원되는 작업에서 [재시도 정책](logic-apps-exception-handling.md#retry-policies)을 설정하여 기본 예외 및 오류 처리를 구현할 수 있습니다. | 기본 제공 작업의 초기 수를 제외하고 원래 실행과 재시도된 각 실행이 계량됩니다. 예를 들어 5번의 재시도로 실행되는 작업은 6번의 실행으로 계량되고 청구됩니다. <p><p>가격 책정은 작업 유형이 기본 제공, 표준 또는 엔터프라이즈인지 여부를 기반으로 합니다. | 포함된 기본 제공 작업을 제외하고는 사용량 모델과 동일합니다. | 계량되거나 청구되지 않습니다. |
||||||

<a name="storage-operations"></a>

## <a name="storage-operations"></a>스토리지 작업

Azure Logic Apps는 트리거 작업을 예약하는 데 큐를 사용하거나 워크플로 상태를 저장하는 데 테이블과 Blob을 사용하는 것과 같은 필요한 모든 스토리지 트랜잭션에 [Azure Storage](../storage/index.yml)를 사용합니다. 워크플로의 작업을 기반으로 하는 경우 다양한 트리거, 작업 및 페이로드로 인해 서로 다른 스토리지 작업 및 요구 사항이 있으므로 스토리지 비용이 달라집니다. 또한 서비스는 논리 앱 리소스의 [실행 기록 보존 제한](logic-apps-limits-and-config.md#run-duration-retention-limits)에 따라 워크플로 실행 기록의 입력 및 출력을 보관하고 저장합니다. 이 보존 제한은 워크플로 수준이 아니라 논리 앱 리소스 수준에서 관리할 수 있습니다.

다음 표에는 사용량, 표준 및 ISE 모델에서 스토리지 작업에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 모델 | 설명 | 계량 및 요금 청구 |
|-------|-------------|----------------------|
| 사용량(다중 테넌트) | 스토리지 리소스 및 사용량이 논리 앱 리소스에 연결됩니다. | 계량 및 청구는 *데이터 보존 관련 스토리지 사용량에만 적용* 되며, [사용량 요금제의 데이터 보존 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 따릅니다. |
| 표준(단일 테넌트) | 사용자 고유의 Azure [스토리지 계정](../azure-functions/storage-considerations.md#storage-account-requirements)을 사용할 수 있으므로 워크플로의 데이터에 대한 더 많은 제어와 유연성을 제공합니다. |  계량 및 청구는 [Azure Storage 가격 책정 모델](https://azure.microsoft.com/pricing/details/storage/)을 따릅니다. 스토리지 비용은 Azure 청구서에 별도로 표시됩니다. <p><p>**팁**: 워크플로에서 실행할 수 있는 스토리지 작업 수와 비용을 더 잘 이해하려면 [Logic Apps 스토리지 계산기](https://logicapps.azure.com/calculator)를 사용해 보세요. 샘플 워크플로를 선택하거나 기존 워크플로 정의를 사용합니다. 첫 번째 계산은 워크플로의 스토리지 작업 수를 예측합니다. 그런 다음, 이러한 수치를 사용하여 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)에서 가능한 비용을 예상할 수 있습니다. 자세한 내용은 [단일 테넌트 Azure Logic Apps 워크플로에 대한 스토리지 요구 사항 및 비용 예측](estimate-storage-costs.md)을 검토하세요. |
| ISE(통합 서비스 환경) | 스토리지 리소스 및 사용량이 논리 앱 리소스에 연결됩니다. | 계량되거나 청구되지 않습니다. |
||||

자세한 내용은 다음 설명서를 검토하세요.

* [실행 및 스토리지 사용량에 대한 메트릭 보기](plan-manage-costs.md#monitor-billing-metrics)
* [Azure Logic Apps의 한도](logic-apps-limits-and-config.md)

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

[온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)는 논리 앱 워크플로에서 특정 게이트웨이 지원 커넥터를 사용하여 온-프레미스 데이터에 액세스할 수 있도록 만드는 별도의 Azure 리소스입니다. 게이트웨이 리소스 자체에는 요금이 발생하지 않지만, 논리 앱에서 사용하는 가격 책정 및 청구 모델에 따라 게이트웨이를 통해 실행되는 작업에는 요금이 발생합니다.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[통합 계정](logic-apps-enterprise-integration-create-integration-account.md)은 거래 파트너, 계약, 스키마, 맵 등과 같은 B2B(Business to Business) 아티팩트를 정의하고 저장하기 위해 컨테이너로 만드는 별도의 Azure 리소스입니다. 이 계정이 만들어지고 이러한 아티팩트가 정의되면 워크플로에서 이러한 아티팩트와 다양한 B2B 작업을 사용하여 [EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 사용하는 통합 솔루션을 검색, 빌드 및 테스트할 수 있도록 이 계정을 논리 앱에 연결합니다.

다음 표에는 사용량, 표준 및 ISE 모델에서 통합 계정에 대한 계량 및 청구를 처리하는 방법이 요약되어 있습니다.

| 모델 | 계량 및 요금 청구 |
|-------|----------------------|
| 사용량(다중 테넌트) | 계량 및 청구는 사용하는 계정 계층에 따라 [통합 계정 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용합니다. |
| 표준(단일 테넌트) | 계량 및 청구는 사용하는 계정 계층에 따라 [통합 계정 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 사용합니다. |
| ISE | 이 모델에는 ISE SKU를 기반으로 하는 단일 통합 계정이 포함됩니다. [추가 비용](https://azure.microsoft.com/pricing/details/logic-apps/)을 지불하면 ISE에서 [총 ISE 제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)까지 사용할 더 많은 통합 계정을 만들 수 있습니다. |
|||

자세한 내용은 다음 설명서를 검토하세요.

* [통합 계정 만들기 및 관리](logic-apps-enterprise-integration-create-integration-account.md)
* [Azure Logic Apps의 통합 계정 제한](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="other-items-not-metered-or-billed"></a>계량 또는 청구되지 않는 기타 항목

모든 가격 책정 모델에서 계량되거나 청구되지 않는 항목은 다음과 같습니다.

* 워크플로가 완료되기 전에 중지되어 실행되지 않은 작업
* 비활성 상태인 동안 새 인스턴스를 만들 수 없으므로 사용하지 않도록 설정된 논리 앱 또는 워크플로

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대한 비용 계획 및 관리](plan-manage-costs.md)
