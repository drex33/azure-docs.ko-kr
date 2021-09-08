---
title: Azure Network Watcher 연결 모니터 스키마 | Microsoft Docs
description: Azure Network Watcher 연결 모니터의 테스트 데이터 스키마 및 경로 데이터 스키마를 이해합니다.
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969768"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Azure Network Watcher 연결 모니터 스키마

연결 모니터는 Azure Network Watcher에서 통합된 엔드투엔드 연결 모니터링을 제공합니다. 연결 모니터 기능은 하이브리드 및 Azure 클라우드 배포를 지원합니다. Network Watcher는 Azure 배포에 대한 연결 관련 메트릭을 모니터링하고 진단하고 볼 수 있는 도구를 제공합니다.

연결 모니터의 일부 사용 사례는 다음과 같습니다.

- 프런트 엔드 웹 서버 VM(가상 머신)이 다중 계층 애플리케이션에서 데이터베이스 서버 VM과 통신합니다. 두 VM 간의 네트워크 연결을 확인하려 합니다.
- 미국 동부 지역의 VM에서 미국 중부 지역의 VM을 ping하고 지역 간 네트워크 대기 시간을 비교하려 합니다.
- 시애틀, 워싱턴 및 애슈번, 버지니아에 여러 온-프레미스 사무실 사이트가 있습니다. 사무실 사이트는 Microsoft 365 URL에 연결됩니다. Microsoft 365 URL 사용자에 대해 시애틀과 애슈번 간의 대기 시간을 비교합니다.
- 하이브리드 애플리케이션에 Azure Storage 엔드포인트에 대한 연결이 필요합니다. 온-프레미스 사이트와 Azure 애플리케이션이 동일한 Azure Storage 엔드포인트에 연결됩니다. 온-프레미스 사이트의 대기 시간을 Azure 애플리케이션의 대기 시간과 비교하려 합니다.
- 온-프레미스 설정과 클라우드 애플리케이션을 호스트하는 Azure VM 간의 연결을 확인하려 합니다.

연결 모니터의 몇 가지 이점은 다음과 같습니다.

* Azure 및 하이브리드 모니터링 요구 사항에 맞는 통합된 직관적인 환경
* 지역 간, 작업 영역 간 연결 모니터링
* 높은 검색 빈도 및 네트워크 성능에 대한 가시성 향상
* 하이브리드 배포에 대한 더 빠른 경고
* HTTP, TCP 및 ICMP를 기반으로 하는 연결 확인 지원 
* Azure 및 비 Azure 테스트 설정 모두에 대한 메트릭 및 로그 분석 지원

Log Analytics에 수집되는 로그 또는 데이터에는 두 가지 유형이 있습니다. 테스트 데이터(NWConnectionMonitorTestResult 쿼리)는 특정 테스트 그룹의 모니터링 빈도에 따라 업데이트됩니다. 경로 데이터(NWConnectionMonitorPathResult 쿼리)는 손실 백분율이나 왕복 시간이 크게 변경될 때 업데이트됩니다. 일정 기간 경로 데이터는 자주 업데이트되지 않는 반면 테스트 데이터는 계속 업데이트될 수 있습니다. 둘 다 독립적이기 때문입니다.

## <a name="connection-monitor-tests-schema"></a>연결 모니터 테스트 스키마

다음 표에는 연결 모니터 테스트 데이터 스키마의 필드와 해당 필드가 나타내는 내용이 나와 있습니다. 

| 필드  |    Description   |
|---|---|
| TimeGenerated | 로그가 생성된 시간의 타임스탬프(UTC) |
| RecordId  | 테스트 결과 레코드의 고유 ID에 대한 레코드 ID |
| ConnectionMonitorResourceId   | 테스트의 연결 모니터 리소스 ID |
| TestGroupName | 테스트가 속한 테스트 그룹 이름 |
| TestConfigurationName | 테스트가 속한 테스트 구성 이름 |
| SourceType    | 테스트에 대해 구성된 원본 머신의 형식 |
| SourceResourceId  | 원본 머신의 리소스 ID |
| SourceAddress | 테스트에 대해 구성된 원본의 주소 |
| SourceSubnet  | 원본의 서브넷 |
| SourceIP  | 원본의 IP 주소 |
| SourceName    | 원본 엔드포인트 이름 |
| SourceAgentId | 원본 에이전트 ID |
| DestinationPort   | 테스트에 대해 구성된 대상 포트 |
| DestinationType   | 테스트에 대해 구성된 대상 머신의 형식 |
| DestinationResourceId | 대상 머신의 리소스 ID |
| DestinationAddress    | 테스트에 대해 구성된 대상의 주소 |
| DestinationSubnet | 해당하는 경우 대상의 서브넷 |
| DestinationIP | 대상의 IP 주소 |
| DestinationName   | 대상 엔드포인트 이름 |
| DestinationAgentId    | 대상 에이전트 ID |
| 프로토콜  | 테스트의 프로토콜 |
| ChecksTotal   | 테스트에서 수행된 총 검사 수 |
| ChecksFailed  | 테스트에서 실패한 총 검사 수 |
| TestResult    | 테스트의 결과 |
| TestResultCriterion   | 테스트의 결과 조건 |
| ChecksFailedPercentThreshold  | 테스트에 대해 설정된 백분율 임계값에 실패한 검사 |
| RoundTripTimeMsThreshold  | 테스트에 대해 설정된 왕복 임계값(밀리초) |
| MinRoundTripTimeMs    | 테스트에 대한 최소 왕복 시간(밀리초) |
| MaxRoundTripTimeMs    | 테스트에 대한 최대 왕복 시간 |
| AvgRoundTripTimeMs    | 테스트에 대한 평균 왕복 시간 |
| JitterMs  | 테스트에 대한 평균 편차 왕복 시간 |
| AdditionalData    | 테스트에 대한 추가 데이터 |


## <a name="connection-monitor-path-schema"></a>연결 모니터 경로 스키마

다음 표에는 연결 모니터 경로 데이터 스키마의 필드와 각 필드가 무엇을 의미하는지가 나와 있습니다. 

| 필드  |    Description   |
|---|---|
| TimeGenerated  | 로그가 생성된 시간의 타임스탬프(UTC) |
| RecordId  | 테스트 결과 레코드의 고유 ID에 대한 레코드 ID |
| TopologyId    | 경로 레코드의 토폴로지 ID |
| ConnectionMonitorResourceId   | 테스트의 연결 모니터 리소스 ID |
| TestGroupName | 테스트가 속한 테스트 그룹 이름 |
| TestConfigurationName | 테스트가 속한 테스트 구성 이름 |
| SourceType    | 테스트에 대해 구성된 원본 머신의 형식 |
| SourceResourceId  | 원본 머신의 리소스 ID |
| SourceAddress | 테스트에 대해 구성된 원본의 주소 |
| SourceSubnet  | 원본의 서브넷 |
| SourceIP  | 원본의 IP 주소 | 
| SourceName    | 원본 엔드포인트 이름 |
| SourceAgentId | 원본 에이전트 ID |
| DestinationPort   | 테스트에 대해 구성된 대상 포트 |
| DestinationType   | 테스트에 대해 구성된 대상 머신의 형식 |
| DestinationResourceId | 대상 머신의 리소스 ID |
| DestinationAddress    | 테스트에 대해 구성된 대상의 주소 |
| DestinationSubnet | 해당하는 경우 대상의 서브넷 |
| DestinationIP | 대상의 IP 주소 |
| DestinationName   | 대상 엔드포인트 이름 |
| DestinationAgentId    | 대상 에이전트 ID |
| 프로토콜  | 테스트의 프로토콜 |
| ChecksTotal   | 테스트에서 수행된 총 검사 수 |
| ChecksFailed  | 테스트에서 실패한 총 검사 수 |
| PathTestResult    | 테스트의 결과 |
| PathResultCriterion   | 테스트의 결과 조건 | 
| ChecksFailedPercentThreshold  | 테스트에 대해 설정된 백분율 임계값에 실패한 검사 |
| RoundTripTimeMsThreshold  | 테스트에 대해 설정된 왕복 임계값(밀리초) |
| MinRoundTripTimeMs    | 테스트에 대한 최소 왕복 시간(밀리초) |
| MaxRoundTripTimeMs    | 테스트에 대한 최대 왕복 시간 |
| AvgRoundTripTimeMs    | 테스트에 대한 평균 왕복 시간 |
| JitterMs  | 테스트에 대한 평균 편차 왕복 시간 |
| HopAddresses | 테스트에 대해 식별된 홉 주소 |
| HopTypes  | 테스트에 대해 식별된 홉 형식 |
| HopLinkTypes  | 테스트에 대해 식별된 홉 링크 형식 |
| HopResourceIds    | 테스트에 대해 식별된 홉 리소스 ID |
| 문제    | 테스트에 대해 식별된 문제 |
| Hops  | 테스트에 대해 식별된 홉 |
| AdditionalData | 테스트에 대한 추가 데이터 |
