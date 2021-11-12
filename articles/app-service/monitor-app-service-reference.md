---
title: App Service 데이터 모니터링 참조
description: App Service를 모니터링할 때 필요한 중요 참조 자료
author: msangapu-msft
ms.topic: reference
ms.author: msangapu
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 914587d60b5932d2f9af6eef400a1a5067974652
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290555"
---
# <a name="monitoring-app-service-data-reference"></a>App Service 데이터 모니터링 참조

이 참조는 App Service 모니터링에 Azure Monitor를 사용하는 경우에 적용됩니다. App Service에 대한 모니터링 데이터의 수집 및 분석 방법 관련 세부 정보는 [App Service 모니터링](monitor-app-service.md)을 참조하세요.

## <a name="metrics"></a>메트릭

이 섹션에는 App Service에 대해 수집되는 자동 수집 플랫폼 메트릭이 모두 나열되어 있습니다.  

|메트릭 유형 | 리소스 공급자/형식 네임스페이스<br/> 및 개별 메트릭 링크 |
|-------|-----|
| App Service 계획 | [Microsoft.Web/serverfarms](../azure-monitor/essentials/metrics-supported.md#microsoftwebserverfarms)
| 웹앱 | [Microsoft.Web/sites](../azure-monitor/essentials/metrics-supported.md#microsoftwebsites) |
| 스테이징 슬롯 | [Microsoft.Web/sites/slots](../azure-monitor/essentials/metrics-supported.md#microsoftwebsitesslots) 
| App Service 환경 | [Microsoft.Web/hostingEnvironments](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironments)
| App Service Environment 프런트 엔드 | [Microsoft.Web/hostingEnvironments/multiRolePools](../azure-monitor/essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)


보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.


## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.

App Service에는 차원이 포함된 메트릭이 없습니다.

## <a name="resource-logs"></a>리소스 로그

이 섹션에는 App Service에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다. 

| 로그 형식 | Windows | Windows 컨테이너 | Linux | Linux 컨테이너 | 설명 |
|-|-|-|-|-|-|
| AppServiceConsoleLogs | Java SE & Tomcat | 예 | 예 | 예 | 표준 출력 및 표준 오류 |
| AppServiceHTTPLogs | 예 | 예 | 예 | 예 | 웹 서버 로그 |
| AppServiceEnvironmentPlatformLogs | 예 | 해당 없음 | 예 | 예 | App Service Environment: 크기 조정, 구성 변경 및 상태 로그|
| AppServiceAuditLogs | 예 | 예 | 예 | 예 | FTP 및 Kudu를 통한 로그인 작업 |
| AppServiceFileAuditLogs | 예 | 예 | TBA | TBA | 사이트 콘텐츠 대상의 파일 변경 내용(**프리미엄 계층 이상에서만 사용 가능**) |
| AppServiceAppLogs | ASP.NET | ASP.NET | Java SE 및 Tomcat 이미지 <sup>1</sup> | Java SE & Tomcat Blessed Images<sup>1</sup> | 애플리케이션 로그 전송 사용 |
| AppServiceIPSecAuditLogs  | 예 | 예 | 예 | 예 | IP 규칙에서 보낸 요청 |
| AppServicePlatformLogs  | TBA | 예 | 예 | 예 | 컨테이너 작업 로그 |
| AppServiceAntivirusScanAuditLogs | 예 | 예 | 예 | 예 | 클라우드 용 Microsoft Defender를 사용 하 여 [바이러스 백신 검사 로그](https://azure.github.io/AppService/2020/12/09/AzMon-AppServiceAntivirusScanAuditLogs.html) **Premium 계층에만 사용할 수 있습니다** . | 

<sup>1</sup>Java SE 앱의 경우 앱 설정에 “$WEBSITE _AZMON_PREVIEW_ENABLED”를 추가하고 1 또는 true로 설정합니다.

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요.

## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블

Azure App Service는 Azure Monitor 로그의 Kusto 테이블을 사용합니다. Log Analytics를 사용하여 해당 테이블을 쿼리할 수 있습니다. Kusto에서 사용하는 App Service 테이블 목록은 [Azure Monitor 로그 테이블 참조 - App Service 테이블](/azure/azure-monitor/reference/tables/tables-resourcetype#app-services)을 참조하세요. 

## <a name="activity-log"></a>활동 로그

다음 표에는 활동 로그에 만들 수 있는 App Service와 관련한 일반적인 작업이 나열되어 있습니다. 전체 목록은 아닙니다.

| 작업(Operation) | Description |
|:---|:---|
|웹앱 만들기 또는 업데이트| 앱을 만들거나 업데이트함|
|웹앱 삭제| 앱이 삭제됨 |
|웹앱 백업 만들기| 앱 백업|
|웹앱 게시 프로필 가져오기| 게시 프로필 다운로드 |
|웹앱 게시| 앱이 배포됨 |
|웹앱 다시 시작| 웹이 다시 시작됨|
|웹앱 시작| 앱이 시작됨 |
|웹 앱 중지| 앱이 중지됨|
|웹앱 슬롯 교환| 슬롯이 교환됨|
|웹 앱 슬롯 차이점 가져오기| 슬롯 차이점|
|웹앱 구성 적용| 적용된 구성 변경 내용|
|웹앱 구성 다시 설정| 구성 변경 내용 다시 설정|
|프라이빗 엔드포인트 연결을 승인합니다.| 승인된 프라이빗 엔드포인트 연결|
|네트워크 추적 Web Apps| 네트워크 추적 시작|
|Newpassword Web Apps| 새 암호가 생성됨 |
|웹앱용 압축 컨테이너 로그 가져오기| 컨테이너 로그 가져오기 |
|백업 Blob에서 웹앱 복원| 백업에서 앱이 복원됨|

활동 로그 항목의 스키마에 대한 자세한 내용은 [활동 로그 스키마](../azure-monitor/essentials/activity-log-schema.md)를 참조하세요. 

## <a name="see-also"></a>참고 항목

- Azure App Service 모니터링에 대한 설명은 [Azure App Service 모니터링](monitor-app-service.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.
