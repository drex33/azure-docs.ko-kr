---
title: 빠른 시작 - Application Insights에 SDK 원격 분석 데이터 내보내기
titleSuffix: An Azure Communication Services quickstart
description: Application Insights에 Azure Communication Services SDK 원격 분석 데이터를 내보내는 방법을 알아봅니다.
author: peiliu
manager: vravikumar
services: azure-communication-services
ms.author: peiliu
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: data
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 71f294cb42977b89fc36d8f750aa43baadb31f47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609501"
---
# <a name="quickstart-using-azure-opentelemetry-exporter-to-export-sdk-telemetry-data-to-application-insights"></a>빠른 시작: Azure OpenTelemetry 내보내기를 사용하여 SDK 원격 분석 데이터를 Application Insights로 내보내기

Azure OpenTelemetry 내보내기는 [Azure Monitor](../../azure-monitor/index.yml) 내의 SDK입니다. OpenTelemetry를 사용하여 추적 데이터를 내보내고 [Application Insights](../../azure-monitor/app/app-insights-overview.md)로 데이터를 보낼 수 있습니다. OpenTelemetry는 애플리케이션 및 프레임워크에서 원격 분석 정보를 수집하는 표준화된 방법을 제공합니다.

Azure Application Insights는 라이브 애플리케이션을 모니터링하는 데 사용되는 Azure Monitor의 기능입니다. 또한 Microsoft Azure 리소스에 애플리케이션에 대한 데이터를 표시합니다. 원격 분석 모델은 플랫폼 및 언어 독립적인 모니터링을 만들 수 있도록 표준화되었습니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/telemetry-app-insights-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/telemetry-app-insights-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/telemetry-app-insights-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/telemetry-app-insights-python.md)]
::: zone-end

앱의 출력은 완료된 각 작업을 설명합니다.
<!---cSpell:disable --->
```console
Created an identity with ID: <identity-id>
Issued an access token with 'chat' scope that expires at <expiry-data>
```
<!---cSpell:enable --->

## <a name="view-the-telemetry-data-in-application-insights"></a>Application Insights에서 원격 분석 데이터 보기
SDK에서 원격 분석 데이터를 분석하기 위해 `Performance` 탭으로 이동한 후 `Dependencies`로 이동합니다. 사용자가 추적한 `Create User Activity` 및 `Get Token Activity`를 볼 수 있습니다.

:::image type="content" source="media/application-insights-dependencies.png" alt-text="Application Insights에서 원격 분석 데이터 항목을 보여 주는 스크린샷":::

좀 더 자세히 알아보려면 샘플을 자세히 살펴보세요.

:::image type="content" source="media/application-insights-samples-drill-down.png" alt-text="샘플의 드릴다운 보기를 보여 주는 스크린샷":::

드릴다운 보기에는 호출된 위치, 타임스탬프, 이름, 성능, 유형 등, 작업에 대한 자세한 정보가 나와 있습니다. 위의 샘플 코드 조각에서 정의한 클라우드 역할 이름 및 인스턴스 ID를 확인할 수도 있습니다. 추적된 사용자 지정 속성도 여기에 표시됩니다.

:::image type="content" source="media/application-insights-e2e-details.png" alt-text="트랜잭션 세부 정보의 종단 간 보기":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음과 같은 방법을 배웠습니다.

> [!div class="checklist"]
> * 원격 분석 내보내기 설정
> * Application Insights로 원격 분석 데이터 이동
> * Application Insights에서 내보낸 데이터 보기

다음을 수행할 수도 있습니다.

- [Application Insights의 데이터 분석에 대한 자세한 정보](/powerapps/maker/canvas-apps/application-insights)