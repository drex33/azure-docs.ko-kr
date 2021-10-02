---
title: Media Services API V3로 마이그레이션하는 이점
description: 이 문서에서는 Media Services v2에서 v3로 마이그레이션하는 이점을 보여 줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 1a733492a46b51d471a0efb0c5f316b0a47c8598
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353158"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>1단계 - Media Services API V3로 마이그레이션하는 이점 이해

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-1.svg)

## <a name="use-the-latest-api"></a>최신 API 사용

새로운 기능, 기능 및 성능 최적화는 현재 V3 API에서만 사용할 수 있으므로 이제 Azure Media Services V3 API 버전 2020-05-01 이상 사용을 시작하여 이점을 얻는 것이 좋습니다.

올바른 버전 문자열을 사용하여 포털, 최신 SDK, 최신 CLI 및 REST API에서 API 버전을 변경할 수 있습니다.

V3에서 Media Services의 기능이 크게 향상되었습니다.  

## <a name="benefits-of-media-services-v3"></a>Media Services v3의 혜택

| **V3 기능** | **이점** |
| --- | --- |
| **Azure Portal** | |
| Azure Portal 업데이트 | V3 API 엔터티 관리를 포함하도록 Azure Portal이 업데이트되었습니다. 이를 통해 고객은 포털을 사용하여 라이브 스트리밍을 시작하고, V3 변환 작업(job)을 제출하고, 콘텐츠 보호 정책을 관리하고, API 액세스 권한을 얻고, 연결된 스토리지 계정을 관리하고, 모니터링 작업(task)을 수행할 수 있습니다. |
| **계정 및 스토리지** | |
| Azure RBAC(역할 기반 액세스 제어) | 이제 고객은 자신의 역할을 정의하고 Media Services ARM API의 각 엔터티에 대한 액세스를 제어할 수 있습니다. 이 기능을 통해 AAD 계정별로 리소스에 대한 액세스를 제어할 수 있습니다. |
| 관리 ID | 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스의 ID를 제공하여 자격 증명을 관리할 필요가 없습니다. [여기](../../active-directory/managed-identities-azure-resources/overview.md)에서 관리 ID의 세부 정보를 참조하세요. |
| 프라이빗 링크 지원 | 고객은 VNet의 PrivateEndpoint를 통해 키 전송, LiveEvents, StreamingEndpoints를 위해 Media Services 엔드포인트에 액세스합니다. |
| [고객 관리형 키](concept-use-customer-managed-keys-byok.md) 또는 BYOK(Bring Your Own Key) 지원 | 고객은 Azure Key Vault의 키를 사용하여 Media Services 계정의 데이터를 암호화할 수 있습니다. |
| **Assets** | |
| 자산에는 각각 다양한 [동적 패키징](encode-dynamic-packaging-concept.md) 및 동적 암호화 설정을 포함하는 여러 [스트리밍 로케이터](stream-streaming-locators-concept.md)가 있을 수 있습니다. | 각 자산에서 허용되는 스트리밍 로케이터는 100개로 제한됩니다. 고객은 자산에 미디어 콘텐츠의 단일 복사본을 저장할 수 있지만, 대상 그룹을 기반으로 하는 다양한 스트리밍 정책 또는 콘텐츠 보호 정책을 포함하는 다양한 스트리밍 URL을 공유할 수 있습니다.
| **작업 처리** ||
| V3은 파일 기반 작업 처리를 위한  [변환](transform-jobs-concept.md)의 개념을 소개합니다. | 변환을 사용하여 재사용 가능한 구성을 빌드하고, Azure Resource Manager 템플릿을 만들고, 여러 고객 또는 테넌트 간에 처리 설정을 격리할 수 있습니다. |
| 파일 기반 작업 처리의 경우 HTTP(S) URL을 입력으로 사용할 수 있습니다. | 아직 콘텐츠를 Azure에 저장하거나 입력 자산을 만들 필요가 없습니다. |
| **라이브 이벤트** ||
| 프리미엄 1080p 라이브 이벤트 | 새 라이브 이벤트 SKU를 사용하면 고객이 최대 1080p 해상도의 출력으로 클라우드 인코딩을 가져올 수 있습니다. |
| [대기 시간이 짧은](live-event-latency-reference.md) 새 라이브 스트리밍이 라이브 이벤트에서 지원됩니다. | 이를 통해 사용자는 해당 설정을 사용하도록 설정하지 않은 경우보다 실시간에 더 가깝게 라이브 이벤트를 볼 수 있습니다. |
| 라이브 이벤트 미리 보기는  [동적 패키징](encode-dynamic-packaging-concept.md) 및 동적 암호화를 지원합니다. | 이렇게 하면 미리 보기와 DASH 및 HLS 패키징에서 콘텐츠 보호가 가능합니다. |
| 라이브 출력이 프로그램을 대체 | 라이브 출력은 v2 API의 프로그램 엔터티보다 사용 방법이 간단합니다. |
| 더 많은 인코더에 대한 지원을 통해 라이브 이벤트에 대한 RTMP 수집이 향상되었습니다. | 안정성을 높이고 소스 인코더 유연성을 제공합니다. |
| 라이브 이벤트는 24x7 스트림 가능 | 라이브 이벤트를 호스트하고 대상 그룹이 더 오랫동안 계속 참여하게 할 수 있습니다. |
| 라이브 이벤트의 라이브 전사 | 라이브 전사를 통해 고객은 라이브 이벤트 브로드캐스트 중에 음성 언어를 실시간으로 텍스트로 자동으로 전사할 수 있습니다. 이렇게 하면 라이브 이벤트의 접근성이 크게 향상됩니다. |
| 라이브 이벤트의 [대기 모드](live-event-outputs-concept.md#standby-mode) | 대기 상태에 있는 라이브 이벤트는 라이브 이벤트를 실행하는 것보다 비용이 적게 듭니다. 이를 통해 고객은 실행 중인 라이브 이벤트 세트를 유지 관리하는 것보다 더 적은 비용으로 몇 초 내에 시작할 준비가 된 라이브 이벤트 세트를 유지 관리할 수 있습니다. 대기 라이브 이벤트의 할인된 가격 책정은 대부분의 지역에서 2021년 2월에 적용되며 나머지는 2021년 4월에 적용됩니다.
|**콘텐츠 보호** ||
| [콘텐츠 보호](drm-content-key-policy-concept.md)는 다중 키 기능을 지원합니다. | 이제 고객은 스트리밍 로케이터에서 여러 콘텐츠 암호화 키를 사용할 수 있습니다. |
| **Monitoring** | |
| [Azure EventGrid](monitoring/reacting-to-media-services-events.md) 알림 지원 | EventGrid 알림은 기능이 더 다양합니다. 더 많은 유형의 알림, 소유한 애플리케이션에서 알림을 수신하기 위한 더 광범위한 SDK 지원 및 이벤트 처리기로 사용할 수 있는 더 많은 기존 Azure 서비스가 있습니다. |
| [Azure Portal의 Azure Monitor 지원 및 통합](monitoring/monitor-events-portal-how-to.md) | 이를 통해 고객은 Media Services 계정 할당량 사용량, 스트리밍 엔드포인트의 실시간 통계를 시각화하고 라이브 이벤트의 통계를 수집 및 보관할 수 있습니다. 이제 고객은 실시간 메트릭 데이터를 기반으로 경고를 설정하고 필요한 작업을 수행할 수 있습니다. |
