---
title: REST API를 사용하여 Azure Chaos Studio 실험 관리
description: REST API를 사용하여 Azure Chaos Studio에서 Chaos 실험을 실행하고 관리합니다.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: edbaeece6a486c170603304dfe328974e6b70879
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102761"
---
# <a name="use-the-chaos-studio-rest-apis-to-run-and-manage-chaos-experiments"></a>Chaos Studio REST API를 사용하여 Chaos 실험 실행 및 관리

> [!WARNING]
> 오류를 삽입하면 애플리케이션 또는 서비스에 영향을 줄 수 있습니다. 고객을 방해하지 않도록 주의하세요.  

Chaos Studio API는 프로그래밍 방식으로 실험을 시작하기 위한 지원을 제공합니다. armclient 및 Azure CLI 사용하여 콘솔에서 이러한 명령을 실행할 수도 있습니다. 아래 예제는 Azure CLI 대한 것입니다.

> [!Warning]
> 이러한 API는 아직 개발 중이며 변경될 수 있습니다.

## <a name="rest-apis"></a>REST API

Squall REST API를 사용하여 실험을 시작 및 중지하고, 대상 상태를 쿼리하고, 실험 상태를 쿼리하고, 구독 구성을 쿼리 및 삭제할 수 있습니다. `AZ CLI`유틸리티를 사용하여 명령줄에서 이러한 작업을 수행할 수 있습니다.

> [!TIP]
> AZ CLI를 사용하여 자세한 출력을 얻으려면 각 명령의 끝에 **--verbose를** 추가합니다. 그러면 디버깅에 도움이 되는 **x-ms-correlation-request-id를** 포함하여 명령이 실행될 때 더 많은 메타데이터가 반환됩니다.

### <a name="chaos-provider-commands"></a>Chaos Provider 명령

#### <a name="enumerate-details-about-the-microsoftchaos-resource-provider"></a>Microsoft.Chaos 리소스 공급자에 대한 세부 정보 열거

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-all-the-operations-of-the-chaos-studio-resource-provider"></a>Chaos Studio 리소스 공급자의 모든 작업 나열

```bash
az rest --method get --url "https://management.azure.com/providers/Microsoft.Chaos/operations?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-chaos-provider-configurations"></a>Chaos 공급자 구성 나열

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/?api-version={apiVersion}" --resource "https://management.azure.com" --verbose 
```

#### <a name="create-chaos-provider-configuration"></a>Chaos 공급자 구성 만들기

```bash
az rest --method put --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/{chaosProviderType}?api-version={apiVersion}" --body @{providerSettings.json} --resource "https://management.azure.com"
```

### <a name="chaos-target-and-agent-commands"></a>Chaos 대상 및 에이전트 명령

#### <a name="list-all-the-targets-or-agents-under-a-subscription"></a>구독에서 모든 대상 또는 에이전트 나열

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos/chaosTargets/?api-version={apiVersion}" --url-parameter "chaosProviderType={chaosProviderType}" --resource "https://management.azure.com"
```

### <a name="chaos-experiment-commands"></a>Chaos 실험 명령

#### <a name="list-all-experiments-in-a-resource-group"></a>리소스 그룹의 모든 실험 나열

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Chaos/chaosExperiments?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-an-experiment-configuration-details-by-name"></a>이름으로 실험 구성 세부 정보 얻기

```bash
az rest --method get --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="create-or-update-an-experiment"></a>실험 만들기 또는 업데이트

```bash
az rest --method put --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --body @{experimentName.json} --resource "https://management.azure.com"
```

#### <a name="delete-an-experiment"></a>실험 삭제

```bash
az rest --method delete --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com" --verbose
```

#### <a name="start-an-experiment"></a>실험 시작

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/start?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-statuses-history-of-an-experiment"></a>실험의 상태(기록) 얻기

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/statuses?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-status-of-an-experiment"></a>실험 상태 보기

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/status?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="cancel-stop-an-experiment"></a>실험 취소(중지)

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/cancel?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-the-last-two-experiment-executions"></a>마지막 두 실험 실행의 세부 정보 나열

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-a-specific-experiment-execution"></a>특정 실험 실행의 세부 정보 나열

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails/{executionDetailsId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

## <a name="parameter-definitions"></a>매개 변수 정의

| 매개 변수 이름 | 정의 | 조회 |
| --- | --- | --- |
| {apiVersion} | 제공된 명령을 실행할 때 사용할 API 버전 | [API 설명서에서](https://aka.ms/chaosrestapi) 찾을 수 있습니다. |
| {experimentId} | 실험에 대한 Azure 리소스 ID | [Chaos Studio 실험 포털 블레이드에서](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments) 찾을 수 있습니다. |
| {chaosProviderType} | Chaos Studio 공급자의 유형 또는 이름 | 사용 가능한 공급자는 현재 공급자 구성 형식 목록에서 찾을 수 [있습니다.](chaos-studio-fault-providers.md) |
| {experimentName.json} | 비정상실제 실험의 구성을 포함하는 JSON | 사용자가 생성 |
| {subscriptionId} | 대상 리소스가 있는 구독 ID | [구독 포털 블레이드에서](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 찾을 수 있습니다. |
| {resourceGroupName} | 대상 리소스가 있는 리소스 그룹의 이름 | [리소스 그룹 포털 블레이드에](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 있을 수 있습니다. |
| {executionDetailsId} | 실험 실행의 실행 ID | [Chaos Studio 실험 포털 블레이드에서](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments) 찾을 수 있습니다. |
