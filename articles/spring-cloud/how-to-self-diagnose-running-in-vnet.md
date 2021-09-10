---
title: Azure Spring Cloud VNET 자체 진단 방법
description: VNET에서 실행되는 Azure Spring Cloud을 자체 진단하고 문제를 해결하는 방법을 알아봅니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 087533d0cc63af3d2c7cadbb45b65c2c4fa2615f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537451"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>VNET에서 실행되는 Azure Spring Cloud 자체 진단
Azure Spring Cloud 진단은 구성 없이 가상 네트워크에서 실행되는 대화형 문제 해결 앱을 지원합니다. 문제를 발견하면 Azure Spring Cloud 진단에서 문제를 식별하고 문제를 해결하는 데 도움이 되는 정보로 안내합니다.

## <a name="navigate-to-the-diagnostics-page"></a>진단 페이지로 이동
다음 절차에서는 네트워크 애플리케이션에 대한 진단을 시작합니다.
1. Azure Portal에 로그인합니다.
1. Azure Spring Cloud 개요 페이지로 이동합니다.
1. 왼쪽 탐색 창 메뉴에서 **진단 및 문제 해결** 을 선택합니다.
1. 세 번째 범주인 **네트워킹** 을 선택합니다.

   ![자체 진단 제목](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

**네트워킹** 범주를 선택한 후에는 VNet 삽입 Azure Spring Cloud에 해당하는 네트워킹과 관련된 두 가지 문제(**DNS 확인** 및 **필수 아웃바운드 트래픽**)를 볼 수 있습니다.

   ![자체 진단 옵션](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

진단 보고서를 보려면 대상 문제를 선택합니다. 진단에 대한 요약은 다음과 같이 표시됩니다.

* *리소스가 제거되었습니다.*
* *리소스가 사용자의 가상 네트워크에 배포되지 않습니다*.

일부 결과에는 관련 문서가 포함되어 있습니다. 다른 서브넷은 결과를 별도로 표시합니다.

## <a name="dns-resolution"></a>DNS 확인

**DNS 확인** 을 선택하면 애플리케이션에 DNS 문제가 있는지 여부가 표시됩니다.  정상적인 앱은 다음과 같이 나열됩니다.

* *서브넷 'subnet01'에 문제 없이 DNS 문제가 해결되었습니다*.
* *서브넷 'subnet02'에 문제 없이 DNS 문제가 해결되었습니다*.

다음 진단 보고서 예제는 애플리케이션의 상태를 알 수 없음을 나타냅니다. 보고 시간 프레임에는 상태가 보고된 시간이 포함되지 않습니다.  컨텍스트 종료 시간을 *2021-03-03T04:20:00Z* 로 가정합니다. **DNS 확인 테이블 렌더링** 의 최신 타임 스탬프는 이전 날짜인 *2021-03-03T03:39:00Z* 입니다. 네트워크가 차단되어 상태 검사 로그가 전송되지 않았을 수 있습니다.

알 수 없는 상태 결과는 관련 설명서를 포함합니다.  왼쪽 꺾쇠 괄호를 선택하면 드롭다운 표시를 볼 수 있습니다.

![DNS 알 수 없음](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

프라이빗 DNS 영역 레코드 집합을 잘못 구성하면 다음과 같은 위험 결과를 얻게 됩니다. `Failed to resolve the Private DNS in subnet xxx`

드롭다운 **DNS 확인 테이블 렌더링** 에서 구성을 확인할 수 있는 세부 메시지 정보를 찾을 수 있습니다.

## <a name="required-outbound-traffic"></a>필수 아웃바운드 트래픽

**필수 아웃바운드 트래픽을** 선택하는 경우 결과는 애플리케이션에 아웃바운드 트래픽 문제가 있는지 여부를 나타냅니다.  정상적인 앱은 다음과 같이 나열됩니다.

* *필수 아웃바운드 트래픽이 'subnet01' 서브넷에서 문제 없이 해결되었습니다.
* *필수 아웃바운드 트래픽이 'subnet02' 서브넷에서 문제 없이 해결되었습니다.

서브넷이 NSG 또는 방화벽 규칙에 의해 차단되고 로그를 차단하지 않은 경우 다음과 같은 오류가 표시됩니다. [고객의 책임](./vnet-customer-responsibilities.md)을 간과했는지 여부를 확인할 수 있습니다.

![엔드포인트 실패](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

30분 내에 `Required Outbound Traffic Table Renderings` 데이터가 없는 경우 결과는 `health status unknown`가 됩니다.
네트워크가 차단되었거나 로그 서비스가 중지된 것 같습니다.

![진단 엔드포인트 알 수 없음](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>추가 정보

* [Azure Spring Cloud 자체 진단 방법](./how-to-self-diagnose-solve.md)
