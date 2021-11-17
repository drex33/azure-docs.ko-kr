---
title: SOAR 콘텐츠 카탈로그 | Azure Sentinel Microsoft Docs
description: 이 문서에서는 플레이북 및 Logic Apps 커넥터를 포함하여 SOAR(보안 오케스트레이션, 자동화 및 응답)를 위해 Azure Sentinel 제공하는 콘텐츠를 표시하고 자세히 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2021
ms.author: yelevin
ms.openlocfilehash: 5f9c68a63c63c3b4cc27b44d697f7577e4dafc3c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517979"
---
# <a name="azure-sentinel-soar-content-catalog"></a>SOAR 콘텐츠 카탈로그 Azure Sentinel

Azure Sentinel SOAR(보안 오케스트레이션, 자동화 및 응답)를 위한 다양한 플레이북과 커넥터를 제공하므로 사용자 환경의 모든 제품 또는 서비스와 Azure Sentinel 쉽게 통합할 수 있습니다.

아래에 나열된 통합에는 다음 구성 요소의 일부 또는 전부가 포함될 수 있습니다.

| 구성 요소 유형 | 용도 | 사용 사례 및 연결된 지침 |
| -------------- | ------- | -------------------------------- |
| **플레이북 템플릿** | 자동화된 워크플로 | 플레이북 템플릿을 사용하여 위협에 자동으로 대응할 수 있는 즉시 사용 가능한 플레이북을 배포합니다.<br><br>[Azure Sentinel의 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md) |
| **Azure Logic Apps 관리 커넥터** | 플레이북을 만들기 위한 구성 요소 | 플레이북은 관리되는 커넥터를 사용하여 수백 개의 Microsoft 및 비 Microsoft 서비스 통신합니다.<br><br>[Logic Apps 커넥터 및 해당 설명서 목록](/connectors/connector-reference/) |
| **사용자 지정 커넥터 Azure Logic Apps** | 플레이북을 만들기 위한 구성 요소 | 미리 제공된 커넥터로 사용할 수 없는 서비스와 통신할 수 있습니다. 사용자 지정 커넥터는 커넥터를 만들고 공유하고 자체 트리거와 작업을 정의할 수 있도록 하여 이러한 요구를 해결합니다.<br><ul><li>[사용자 지정 커넥터 개요](/connectors/custom-connectors/)<li>[고유한 사용자 지정 Logic Apps 커넥터 만들기](/connectors/custom-connectors/create-logic-apps-connector) |
|

SOAR 통합 및 해당 구성 요소는 다음 위치에 있습니다.

- Azure Sentinel 솔루션
- Azure Sentinel Automation 블레이드, 플레이북 템플릿 탭
- Logic Apps 디자이너(관리되는 Logic Apps 커넥터용)
- Azure Sentinel GitHub 리포지토리

> [!TIP]
> - 많은 SOAR 통합은 관련 데이터 커넥터, 분석 규칙 및 통합 문서와 함께 [Azure Sentinel 솔루션의](sentinel-solutions.md)일부로 배포할 수 있습니다. 자세한 내용은 [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조하세요.
> - Azure Sentinel 커뮤니티에서 더 많은 통합을 제공하며 GitHub 리포지토리에서 찾을 수 있습니다.
> - 나열되지 않았거나 현재 지원되지 않는 제품 또는 서비스가 있는 경우 기능 요청을 제출하세요.  
> 다음 도구를 사용하여 직접 만들 수도 있습니다.
>    - Logic Apps 사용자 지정 커넥터
>    - Azure 기능
>    - HTTP 호출 Logic Apps


## <a name="atlassian"></a>Atlassian

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Jira** | [관리되는 Logic Apps 커넥터](/connectors/jira/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 동기화 인시던트 |
|

## <a name="check-point"></a>Check Point

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Check Point NGFW**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#check-point) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | Checkpoint |  |
|

## <a name="cisco"></a>시스코

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Cisco ASA, <br> Cisco Meraki** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | IP 차단 |
| **Cisco FirePower** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | IP 및 URL 차단 |
| **Cisco ISE**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#cisco) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | Microsoft |  |
| **Cisco Umbrella**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#cisco) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | Microsoft | 도메인 차단, <br>정책 관리, <br>대상 목록 관리, <br>보강 및 조사 |
|

## <a name="crowdstrike"></a>2018년 3월

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **끊어지게 엔드포인트 보호**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#crowdstrike) | 플레이북 | Microsoft | 엔드포인트 보강,<br>엔드포인트 격리 |
|

## <a name="f5"></a>F5

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Big-IP** | 플레이북 | 커뮤니티 | IP 및 URL 차단 |
|

## <a name="forcepoint"></a>Forcepoint

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Forcepoint NGFW** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | IP 및 URL 차단 |
|

## <a name="fortinet"></a>Fortinet

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **FortiGate**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#fortinet-fortigate) | 사용자 지정 Logic Apps 커넥터<br><br>Azure Function<br><br>플레이북 | Microsoft | IP 및 URL 차단 |
|

## <a name="freshdesk"></a>Freshdesk

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Freshdesk** | [관리되는 Logic Apps 커넥터](/connectors/freshdesk/) |  | 동기화 인시던트 |
|


## <a name="have-i-been-pwned"></a>Have I Been Pwned

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Have I Been Pwned** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 |  |
|

## <a name="hyas"></a>HYAS

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **HYAS 인사이트**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#hyas) | [관리되는 Logic Apps 커넥터](/connectors/hyasinsight/)<br><br>플레이북 | HYAS |  |
|

## <a name="ibm"></a>IBM

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **탄력** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | 동기화 인시던트 |
|

## <a name="microsoft"></a>Microsoft

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Azure DevOps** | 관리되는 Logic Apps 커넥터<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 동기화 인시던트 |
| **Azure Firewall**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#azure) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | Microsoft | IP 차단 |
| **Azure AD ID 보호** | [관리되는 Logic Apps 커넥터](/connectors/azureadip/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 사용자 보강, <br>사용자 수정 |
| **Azure AD** | [관리되는 Logic Apps 커넥터](/connectors/azuread/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 사용자 보강, <br>사용자 수정 |
| **Azure Data Explorer** | [관리되는 Logic Apps 커넥터](/connectors/kusto/) | Microsoft | 쿼리 및 조사 |
| **Azure Log Analytics Data Collector** | [관리되는 Logic Apps 커넥터](/connectors/azureloganalyticsdatacollector/) | Microsoft<br><br>커뮤니티 | 쿼리 및 조사 |
| **엔드포인트에 대한 Microsoft Defender** | [관리되는 Logic Apps 커넥터](/connectors/wdatp/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 엔드포인트 보강, <br>엔드포인트 격리 |
| **Microsoft 팀** | [관리되는 Logic Apps 커넥터](/connectors/teams/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 알림을 <br>공동 작업 <br>사람이 참여하는 응답 만들기 |
|

## <a name="okta"></a>Okta

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Okta** | 관리되는 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | 사용자 보강, <br>사용자 수정 |
|

## <a name="palo-alto"></a>Palo Alto

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Palo Alto PAN-OS**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#palo-alto) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | IP 및 URL 차단 |
| **은(는)** | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | Filehash 보강 및 응답 |
|

## <a name="proofpoint"></a>Proofpoint

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Proofpoint TAP**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#proofpoint) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | Microsoft | 계정 보강 |
|

## <a name="recorded-future"></a>기록된 미래

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **기록된 미래 인텔리전스** | [관리되는 Logic Apps 커넥터](/connectors/recordedfuture/)<br><br>플레이북 | 기록된 미래 | 엔터티 보강 |
|

## <a name="reversinglabs"></a>ReversingLabs

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **TitaniumCloud 파일 보강**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#reversinglabs) | [관리되는 Logic Apps 커넥터](/connectors/reversinglabsintelligence/)<br><br>플레이북 | ReversingLabs | FileHash 보강 |
|

## <a name="riskiq"></a>RiskIQ

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **RiskIQ 디지털 공간**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#riskiq) | [관리되는 Logic Apps 커넥터](/connectors/riskiqdigitalfootprint/)<br><br>플레이북 | RiskIQ | 엔터티 보강 |
| **RiskIQ Passive Total** | [관리되는 Logic Apps 커넥터](/connectors/riskiqpassivetotal/)<br><br>플레이북 | RiskIQ | 엔터티 보강 |
| **RiskIQ 보안 인텔리전스**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#riskiq) | [관리되는 Logic Apps 커넥터](/connectors/riskiqintelligence/)<br><br>플레이북 | RiskIQ | 엔터티 보강 |
|

## <a name="servicenow"></a>ServiceNow

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **ServiceNow** | [관리되는 Logic Apps 커넥터](/connectors/service-now/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 동기화 인시던트 |
|

## <a name="slack"></a>Slack

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Slack** | [관리되는 Logic Apps 커넥터](/connectors/slack/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 알림 <br>협업 |
|

## <a name="virus-total"></a>Virus Total

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Virus Total** | [관리되는 Logic Apps 커넥터](/connectors/virustotal/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 엔터티 보강 |
|

## <a name="vmware"></a>VMware

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Carbon Black Cloud**<br>([솔루션으로 사용 가능)](sentinel-solutions-catalog.md#vmware) | 사용자 지정 Logic Apps 커넥터<br><br>플레이북 | 커뮤니티 | 엔드포인트 보강, <br>엔드포인트 격리 |
|

## <a name="zendesk"></a>Zendesk

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Zendesk** | [관리되는 Logic Apps 커넥터](/connectors/zendesk/)<br><br>플레이북 | Microsoft<br><br>커뮤니티 | 동기화 인시던트 |
|

## <a name="zscaler"></a>Zscaler

| 제품 | 통합 구성 요소 | 지원 요소 | 시나리오 |
| --- | --- | --- | --- |
| **Zscaler** | 플레이북 | Microsoft | URL 수정, <br>인시던트 보강 |
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 솔루션과 이를 찾아 배포하는 방법에 대해 알아봅니다.

- [Azure Sentinel 솔루션](sentinel-solutions.md)에 대해 자세히 알아봅니다.
- [Azure Sentinel 솔루션을 찾아 배포합니다](sentinel-solutions-deploy.md).
