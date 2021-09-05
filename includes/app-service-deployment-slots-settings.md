---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: ae6e4f523eb67d6ff92a3d94107a8e8fa154770a
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598632"
---
다른 배포 슬롯으로부터 구성을 복제할 때 복제된 구성을 편집할 수 있습니다. 교환 후(특정 슬롯) 다른 구성 요소는 동일한 슬롯에 남아 있지만 일부 구성 요소는 교환(특정 슬롯 아님)에 따라 콘텐츠를 따릅니다. 다음 목록은 슬롯을 교환할 때 변경되는 설정을 보여줍니다.

**교환된 설정**:

* 프레임워크 버전, 32/64비트, 웹 소켓과 같은 일반 설정
* 앱 설정(슬롯에 맞도록 구성할 수 있음)
* 연결 설정(슬롯에 맞도록 구성할 수 있음)
* 처리기 매핑
* 공용 인증서
* WebJob 콘텐츠
* 하이브리드 연결 *
* 서비스 엔드포인트 *
* Azure Content Delivery Network *
* 경로 매핑

별표(*)로 표시된 기능은 교환되지 않도록 계획됩니다. 

**교환되지 않은 설정**:

* 게시 엔드포인트
* 사용자 지정 도메인 이름
* 공용이 아닌 인증서 및 TLS/SSL 설정
* 크기 조정 설정
* WebJob 스케줄러
* IP 제한
* Always On
* 진단 설정
* CORS(원본 간 리소스 공유)
* 가상 네트워크 통합
* 관리 ID
* 접미사 _EXTENSION_VERSION으로 끝나는 설정

> [!NOTE]
> 이러한 설정이 교환 가능하도록 하려면 앱의 모든 슬롯에 앱 설정 `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`를 추가하고 그 값을 `0` 또는 `false`로 설정합니다. 이러한 설정은 모두 교환 가능하거나 교환할 수 없습니다. 일부 설정만 교환 가능하도록 할 수는 없습니다. 관리 ID는 교환되지 않으며 이 재정의 앱 설정의 영향을 받지 않습니다.

> 교환되지 않는 설정에 적용되는 특정 앱 설정 역시 교환되지 않습니다. 예를 들어 진단 설정은 교환되지 않으므로 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` 및 `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS`와 같은 관련 앱 설정 역시 슬롯 설정에 표시되지 않더라도 교환되지 않습니다.
>
