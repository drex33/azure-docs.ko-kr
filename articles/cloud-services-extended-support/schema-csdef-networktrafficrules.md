---
title: Azure Cloud Services(추가 지원) Def. NetworkTrafficRules 스키마 | Microsoft Docs
description: Cloud Services(추가 지원)와 연결된 네트워크 트래픽 규칙과 관련된 정보
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0064794701e87419da086c458673f7ccee4f37dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744553"
---
# <a name="azure-cloud-services-extended-support-definition-networktrafficrules-schema"></a>Azure Cloud Services(추가 지원) 정의 NetworkTrafficRules 스키마

`NetworkTrafficRules` 노드는 역할이 서로 통신하는 방법을 지정하는 서비스 정의 파일의 선택적 요소입니다. 특정 역할의 내부 엔드포인트에 액세스할 수 있는 역할을 제한합니다. `NetworkTrafficRules`는 독립 실행형 요소가 아닙니다. 서비스 정의 파일에 두 개 이상의 역할과 결합됩니다.

서비스 정의 파일의 기본 확장명은 csdef입니다.

> [!NOTE]
>  `NetworkTrafficRules` 노드는 Azure SDK 버전 1.3 이상이어야 사용할 수 있습니다.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>네트워크 트래픽 규칙에 대한 기본 서비스 정의 스키마
네트워크 트래픽 정의를 포함하는 서비스 정의 파일의 기본 형식은 다음과 같습니다.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>스키마 요소
서비스 정의 파일의 `NetworkTrafficRules` 노드는 이 토픽의 후속 섹션에서 자세히 설명하는 이러한 요소를 포함합니다.

[NetworkTrafficRules 요소](#NetworkTrafficRules)

[OnlyAllowTrafficTo 요소](#OnlyAllowTrafficTo)

[Destinations 요소](#Destinations)

[RoleEndpoint 요소](#RoleEndpoint)

[AllowAllTraffic 요소](#AllowAllTraffic)

[WhenSource 요소](#WhenSource)

[FromRole 요소](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a> NetworkTrafficRules 요소
ph x="1" /&gt; 요소는 다른 역할의 엔드포인트와 통신할 수 있는 역할을 지정합니다. 서비스에는 하나의 `NetworkTrafficRules` 정의가 포함될 수 있습니다.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> OnlyAllowTrafficTo 요소
ph x="1" /&gt; 요소는 대상 엔드포인트의 컬렉션 및 대상 엔드포인트와 통신할 수 있는 역할을 설명합니다. 여러 `OnlyAllowTrafficTo` 노드를 지정할 수 있습니다.

##  <a name="destinations-element"></a><a name="Destinations"></a> Destinations 요소
`Destinations` 요소는 통신할 수 있는 RoleEndpoints의 컬렉션을 설명합니다.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a> RoleEndpoint 요소
ph x="1" /&gt; 요소는 역할에서 통신을 허용하는 엔드포인트를 설명합니다. 역할에 둘 이상의 엔드포인트가 있는 경우 `RoleEndpoint` 요소를 여러 개 지정할 수 있습니다.

| attribute      | Type     | Description |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | 필수 요소. 트래픽을 허용하는 엔드포인트의 이름입니다.|
| `roleName`     | `string` | 필수 요소. 통신을 허용하는 웹 역할의 이름입니다.|

## <a name="allowalltraffic-element"></a><a name="AllowAllTraffic"></a> AllowAllTraffic 요소
ph x="1" /&gt; 요소는 모든 역할이 `Destinations` 노드에 정의된 엔드포인트와 통신하도록 허용하는 규칙입니다.

##  <a name="whensource-element"></a><a name="WhenSource"></a> WhenSource 요소
ph x="1" /&gt; 요소는 `Destinations` 노드에 정의된 엔드포인트와 통신할 수 있는 역할의 컬렉션을 설명합니다.

| attribute | Type     | Description |
| --------- | -------- | ----------- |
| `matches` | `string` | 필수 요소. 통신을 허용할 때 적용할 규칙을 지정합니다. 현재 유효한 값은 `AnyRule`뿐입니다.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a> FromRole 요소
ph x="1" /&gt; 요소는 `Destinations` 노드에 정의된 엔드포인트와 통신할 수 있는 역할을 지정합니다. 엔드포인트와 통신할 수 있는 역할이 둘 이상인 경우 `FromRole` 요소를 여러 개 지정할 수 있습니다.

| attribute  | Type     | Description |
| ---------- | -------- | ----------- |
| `roleName` | `string` | 필수 요소. 통신을 허용하는 역할에 대한 이름입니다.|

## <a name="see-also"></a>참고 항목
[클라우드 서비스(추가 지원) 정의 스키마](schema-csdef-file.md).




