---
title: 프라이빗 가용성 테스트 - Azure Monitor Application Insights
description: 프라이빗 테스트를 사용하여 방화벽 뒤에서 실행되는 내부 서버에서 가용성 테스트를 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 713a57db66042c562e212d65c6dd265c2aab47cd
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858446"
---
# <a name="private-testing"></a>프라이빗 테스트

방화벽 뒤에서 실행되는 내부 서버에서 가용성 테스트를 사용하려는 경우 공용 ping 테스트 사용 설정 및 연결 끊김/수신 없음 시나리오의 두 가지 가능한 솔루션이 있습니다.

## <a name="public-ping-test-enablement"></a>공용 ping 테스트 사용 설정

> [!NOTE]
> 환경에 대한 수신을 허용하지 않으려면 [연결 끊김 또는 수신 없음 시나리오](#disconnected-or-no-ingress-scenarios) 섹션의 방법을 사용합니다.

 내부 웹 사이트에 대한 공용 DNS 레코드가 있는지 확인합니다. DNS를 확인할 수 없는 경우 테스트가 실패합니다. [내부 애플리케이션을 위한 사용자 지정 도메인 이름을 만듭니다.](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

Microsoft 서비스에서 들어오는 요청을 허용하도록 방화벽을 구성합니다.

- [서비스 태그](../../virtual-network/service-tags-overview.md)는 개별 IP를 승인하거나 최신 목록을 유지하지 않고도 Azure 서비스를 사용하도록 설정할 수 있는 간단한 방법입니다. 서비스 태그는 서비스 액세스를 허용하기 위해 Azure Firewall 및 네트워크 보안 그룹에서 사용할 수 있습니다. **ApplicationInsightsAvailability** 는 ping 테스트 서비스 전용 서비스 태그입니다.
    1. [Azure 네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)을 사용하는 경우 네트워크 보안 그룹 리소스로 이동하여 *설정* 에서 **인바운드 보안 규칙** 을 선택한 다음 **추가** 를 선택합니다.

         :::image type="content" source="media/availability-private-test/add.png" alt-text="네트워크 보안 그룹 리소스의 인바운드 보안 규칙 탭의 스크린샷":::

    1. 그런 다음 *서비스 태그* 를 원본으로 선택하고 *ApplicationInsightsAvailability* 를 원본 서비스 태그로 선택합니다. 서비스 태그에서 들어오는 트래픽에 대한 포트 80(http) 및 443(https) 열기를 사용합니다.

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="서비스 태그 원본이 있는 인바운드 보안 규칙 추가 탭의 스크린샷":::

- 엔드포인트가 Azure 외부에서 호스팅되거나 시나리오에 서비스 태그를 사용할 수 없는 경우 [웹 테스트 에이전트의 IP 주소](ip-addresses.md)를 개별적으로 허용 목록에 추가해야 합니다. [서비스 태그 API](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)를 사용하여 PowerShell, Azure CLI 또는 REST 호출에서 직접 IP 범위를 쿼리할 수 있습니다. [JSON 파일](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 다운로드하여 IP 주소 세부 정보와 함께 현재 서비스 태그 목록을 가져올 수도 있습니다.
    1. 네트워크 보안 그룹 리소스에서 *설정* 아래의 **인바운드 보안 규칙** 을 선택한 다음 **추가** 를 선택합니다.
    1. 그런 다음 원본으로 *IP 주소* 를 선택한 다음 원본 IP 주소/CIRD 범위에서 쉼표로 구분된 목록에 IP 주소를 추가합니다.

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="IP 주소 원본이 있는 인바운드 보안 규칙 추가 탭의 스크린샷":::

## <a name="disconnected-or-no-ingress-scenarios"></a>연결 끊김 또는 수신 없음 시나리오

테스트 서버에 Application Insights 수집 엔드포인트에 대한 송신 액세스 권한이 있어야 하며, 이는 들어오는 요청을 허용하는 대체 방법보다 보안 위험이 훨씬 낮습니다. 이 결과는 Azure Portal을 통해 생성된 테스트에 사용할 수 있는 간소화된 환경과 함께 가용성 웹 테스트 탭에 나타납니다. 또한 사용자 지정 가용성 테스트는 분석, 검색 및 메트릭에 가용성 결과로 표시됩니다.

1. [Azure Private Link](../logs/private-link-security.md)를 사용하여 Application Insights 리소스와 연결이 끊긴 환경을 연결합니다.
1. 사용자 지정 코드를 작성하여 내부 서버 또는 엔드포인트를 주기적으로 테스트합니다. 방화벽 뒤의 테스트 서버에서 [Azure Functions](availability-azure-functions.md) 또는 백그라운드 프로세스를 사용하여 코드를 실행할 수 있습니다. 테스트 프로세스는 코어 SDK 패키지의 `TrackAvailability()` API를 사용하여 Application Insights에 결과를 보낼 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md)

## <a name="next-steps"></a>다음 단계

* [Azure Private Link](../logs/private-link-security.md)
* [가용성 경고](availability-alerts.md)
* [URL 테스트](monitor-web-app-availability.md)
* [Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행](availability-azure-functions.md)
