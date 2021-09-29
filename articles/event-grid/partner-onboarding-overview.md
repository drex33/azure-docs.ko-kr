---
title: 파트너 온보딩 개요(Azure Event Grid)
description: Event Grid 파트너로 온보딩할 수 있는 방법에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: 0c4c43673a0ae2f8094f176e57c203051fd0bea4
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233146"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>파트너 온보딩 개요(Azure Event Grid)

이 문서에서는 Azure Event Grid 파트너 리소스를 비공개로 사용하는 방법과 공개적으로 사용할 수 있는 파트너 토픽 유형이 되는 방법을 설명합니다.

Event Grid 파트너로 게시 이벤트와 연결된 Event Grid 리소스 유형을 사용하는 데는 특별한 권한이 필요하지 않습니다. 실제로 파트너가 되는 것을 고려 중인 경우 지금 바로 이를 사용하여 이벤트를 자체 Azure 구독에 비공개로 게시하고 리소스 모델을 테스트할 수 있습니다.

> [!NOTE]
> Azure Portal을 사용하여 Event Grid 파트너로 온보딩하는 방법에 대한 단계별 지침은 [Event Grid 파트너로 온보딩하는 방법(Azure Portal)](onboard-partner.md)을 참조하세요. 

## <a name="how-partner-events-work"></a>파트너 이벤트 작동 방식
파트너 이벤트 기능은 Event Grid에서 Azure Storage 및 Azure IoT Hub와 같은 Azure 리소스의 이벤트를 게시하는 데 사용하는 기존 아키텍처로 구성되며, 이러한 도구를 누구나 공개적으로 사용할 수 있도록 합니다. 이러한 도구를 사용하는 것은 기본적으로 비공개의 Azure 구독 전용입니다. 이벤트를 공개적으로 사용할 수 있도록 하려면 양식을 작성하고 [Event Grid 팀에 문의](mailto:gridpartner@microsoft.com)하세요.

파트너 이벤트 기능을 사용하면 Azure Event Grid에 다중 테넌트 사용에 대한 이벤트를 게시할 수 있습니다.

## <a name="onboarding-and-event-publishing-overview"></a>온보딩 및 이벤트 게시 개요

### <a name="partner-flow"></a>파트너 흐름

1. 아직 Azure 테넌트가 없는 경우 새로 하나 만듭니다.
1. Azure CLI를 사용하여 새 Event Grid `partnerRegistration`을 만듭니다. 이 리소스에는 표시 이름, 설명, 설치 URI 등의 정보가 포함됩니다.

    ![파트너 토픽 만들기](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 이벤트를 게시할 각 지역에 하나 이상의 파트너 네임스페이스를 만듭니다. Event Grid 서비스는 공개 엔드포인트(예: `https://contoso.westus-1.eventgrid.azure.net/api/events`) 및 액세스 키를 프로비저닝합니다.

    ![파트너 네임페이스 만들기](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 고객이 파트너 토픽을 원하는 시스템에 등록할 수 있는 방법을 제공합니다.
1. Event Grid 팀에 연락하여 파트너 토픽 유형이 공개되기를 원한다고 알립니다.

### <a name="customer-flow"></a>고객 흐름

1. 고객이 Azure Portal을 방문하여 파트너 토픽을 만들려는 Azure 구독 ID 및 리소스 그룹을 기록합니다.
1. 고객이 시스템을 통해 파트너 토픽을 요청합니다. 이에 대한 응답으로 파트너 네임스페이스에 대한 이벤트 터널을 만듭니다.
1. Event Grid는 고객의 Azure 구독 및 리소스 그룹에 **보류 중** 인 파트너 토픽을 만듭니다.

    ![이벤트 채널 만들기](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 고객이 Azure Portal을 통해 파트너 토픽을 활성화합니다. 이제 이벤트가 서비스에서 고객의 Azure 구독으로 전달될 수 있습니다.

    ![파트너 토픽 활성화](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>리소스 모델
다음 리소스 모델은 파트너 이벤트용입니다.

### <a name="partner-registrations"></a>파트너 등록
* 리소스: `partnerRegistrations`
* 사용 대상: 파트너
* 설명: SaaS(software as a service) 파트너의 글로벌 메타데이터(예: 이름, 표시 이름, 설명, 설치 URI)를 캡처합니다.
    
    파트너 등록을 만들거나 업데이트하는 작업은 파트너를 위한 셀프 서비스 작업입니다. 이 셀프 서비스 기능을 통해 파트너는 전체 엔드투엔드 흐름을 빌드하고 테스트할 수 있습니다.
    
    고객은 Microsoft 승인 파트너 등록만 검색할 수 있습니다.
* 범위: 파트너의 Azure 구독에서 만들어집니다. 메타데이터는 공개된 후 고객에게 표시됩니다.

### <a name="partner-namespaces"></a>파트너 네임스페이스
* 리소스: `partnerNamespaces`
* 사용 대상: 파트너
* 설명: 고객 이벤트를 게시하기 위한 지역별 리소스를 제공합니다. 각 파트너 네임스페이스에는 게시 엔드포인트 및 인증 키가 있습니다. 또한 네임스페이스는 파트너가 지정된 고객에 대한 파트너 토픽을 요청하고 활성 고객을 나열하는 방법입니다.
* 범위: 파트너의 구독에 있습니다.

### <a name="event-channel"></a>이벤트 채널
* 리소스: `partnerNamespaces/eventChannels`
* 사용 대상: 파트너
* 설명: 이벤트 채널은 고객의 파트너 토픽을 반영합니다. 이벤트 채널을 만들고 메타데이터에서 고객의 Azure 구독 및 리소스 그룹을 지정하면 Event Grid에 고객에 대한 파트너 토픽을 만들라는 신호를 보냅니다. Event Grid는 Azure Resource Manager 호출을 실행하여 고객의 구독에서 해당 파트너 토픽을 만듭니다. 파트너 토픽은 보류 중 상태로 생성됩니다. 각 이벤트 채널과 파트너 토픽 사이에는 일대일 링크가 있습니다.
* 범위: 파트너의 구독에 있습니다.

### <a name="partner-topics"></a>파트너 토픽
* 리소스: `partnerTopics`
* 사용 대상: 고객
* 설명: 파트너 토픽은 Event Grid의 사용자 지정 항목 및 시스템 항목과 유사합니다. 각 파트너 토픽은 특정 원본(예: `Contoso:myaccount`) 및 특정 파트너 토픽 유형(예: Contoso)과 연결됩니다. 고객은 파트너 토픽에 이벤트 구독을 만들어 이벤트를 다양한 이벤트 처리기로 라우팅합니다.

    고객은 이 리소스를 직접 만들 수 없습니다. 파트너 토픽을 만드는 유일한 방법은 이벤트 채널을 만드는 파트너 작업을 통하는 것입니다.
* 범위: 고객의 구독에 있습니다.

### <a name="partner-topic-types"></a>파트너 토픽 유형
* 리소스: `partnerTopicTypes`
* 사용 대상: 고객
* 설명: 파트너 토픽 유형은 고객이 승인된 파트너 토픽 유형 목록을 검색할 수 있도록 하는 테넌트 전체 리소스 유형입니다. URL은 https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes) 와 같습니다.
* 범위: Global

## <a name="publish-events-to-event-grid"></a>Event Grid에 이벤트 게시
Azure 지역에서 파트너 네임스페이스를 만들면 지역 엔드포인트 및 해당 인증 키가 제공됩니다. 해당 네임스페이스의 모든 고객 이벤트 채널에 대해 이 엔드포인트에 이벤트 일괄 처리를 게시합니다. 이벤트의 원본 필드에 따라 Azure Event Grid는 각 이벤트를 해당 파트너 토픽에 매핑합니다.

### <a name="event-schema-cloudevents-v10"></a>이벤트 스키마: CloudEvents v1.0
CloudEvents 1.0 스키마를 사용하여 Azure Event Grid에 이벤트를 게시합니다. Event Grid는 구조적 모드와 일괄 처리 모드를 모두 지원합니다. CloudEvents 1.0은 파트너 네임스페이스에 대해 유일하게 지원되는 이벤트 스키마입니다.

### <a name="example-flow"></a>흐름 예

1.  게시 서비스가 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`에 대한 HTTP POST를 수행합니다.
1.  요청에 인증을 위한 키를 포함하며 이름이 aeg-sas-key인 헤더를 포함합니다. 이 키는 파트너 네임스페이스를 만드는 동안 프로비저닝됩니다. 예를 들어 유효한 헤더 값은 aeg-sas-key (VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==)입니다.
1.  Content-Type 헤더를 "application/cloudevents-batch+json; charset=UTF-8a"로 설정합니다.
1.  해당 지역의 이벤트를 일괄 처리하여 게시 URL에 대한 HTTP POST 쿼리를 실행합니다. 예를 들면 다음과 같습니다.

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

파트너 네임스페이스 엔드포인트에 게시한 후 응답을 수신합니다. 응답은 표준 HTTP 응답 코드입니다. 몇 가지 일반적인 응답은 다음과 같습니다.

| 결과                             | 응답              |
|------------------------------------|-----------------------|
| Success                            | 200 정상                |
| 이벤트 데이터의 형식이 잘못되었습니다.    | 400 잘못된 요청       |
| 잘못된 액세스 키                 | 401 권한 없음      |
| 잘못된 엔드포인트                 | 404 찾을 수 없음         |
| 배열 또는 이벤트가 크기 제한을 초과합니다. | 413 페이로드가 너무 큼 |

## <a name="references"></a>참조

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 템플릿](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 템플릿 스키마](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLI 확장](/cli/azure/eventgrid)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>다음 단계
- [파트너 토픽 개요](partner-events-overview.md)
- [파트너 토픽 온보딩 양식](https://aka.ms/gridpartnerform)
- [Auth0 파트너 토픽](auth0-overview.md)
- [Auth0 파트너 토픽을 사용하는 방법](auth0-how-to.md)
