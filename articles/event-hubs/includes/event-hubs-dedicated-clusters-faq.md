---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/28/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 024c6d3e428d78ef4be4c42578f407b09148e001
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210721"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>클러스터로 무엇을 수행할 수 있나요?

Event Hubs 클러스터의 경우 수집 및 스트림할 수 있는 양은 생산자, 소비자, 수집 및 처리 속도 등과 같은 다양한 요인에 따라 달라집니다. 

다음 표에는 테스트를 통해 실현한 벤치마크 결과가 나와 있습니다.

| 페이로드 셰이프 | 수신기 | 수신 대역폭| 수신 메시지 | 송신 대역폭 | 송신 메시지 | 총 TU | CU당 TU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB의 일괄 처리 | 2 | 400MB/초 | 초당 400k 메시지 | 800MB/초 | 초당 800k 메시지 | 400TU | 100TU | 
| 10x10KB의 일괄 처리 | 2 | 666MB/초 | 초당 66.6k 메시지 | 1.33GB/초 | 초당 133k 메시지 | 666TU | 166TU |
| 6x32KB의 일괄 처리 | 1 | 1.05GB/초 | 초당 34k 메시지 | 1.05GB/초 | 초당 34k 메시지 | 1,000TU | 250TU |

테스트에 사용된 조건은 다음과 같습니다.

- 4개의 CU(용량 단위)가 있는 전용 계층 Event Hubs 클러스터가 사용되었습니다. 
- 수집에 사용된 이벤트 허브에는 200개의 파티션이 있었습니다. 
- 데이터는 모든 파티션으로부터 받는 두 개의 수신기 애플리케이션에서 수집되었습니다.

### <a name="can-i-scale-updown-my-cluster"></a>내 클러스터를 확장/축소할 수 있나요?

생성 후에는 클러스터는 최소 4시간의 사용량에 대해 청구됩니다. 셀프 서비스 환경의 미리 보기 릴리스에서는 **기술** > **할당량** > **전용 클러스터 확장 또는 축소를 위한 요청** 에 따라 Event Hubs 팀에 [지원 요청](https://ms.portal.azure.com/#create/Microsoft.Support)을 제출하여 클러스터를 확장하거나 축소할 수 있습니다. 클러스터 축소 요청을 완료하는 데 최대 7일이 걸릴 수 있습니다. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Geo-DR은 내 클러스터에서 어떻게 작동하나요?

전용 계층 클러스터 아래의 네임스페이스를 전용 계층 클러스터 아래의 다른 네임스페이스와 지리적으로 페어링할 수 있습니다. 처리량 제한이 호환되지 않아 오류가 발생하므로 전용 계층 네임스페이스를 표준 제품의 네임스페이스와 페어링하지 않는 것이 좋습니다. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>내 표준 네임스페이스를 전용 계층 클러스터에 속하도록 마이그레이션할 수 있나요?
현재 표준 네임스페이스에서 전용 네임스페이스로 이벤트 허브 데이터를 마이그레이션하기 위한 자동화된 마이그레이션 프로세스를 지원하지 않습니다. 
