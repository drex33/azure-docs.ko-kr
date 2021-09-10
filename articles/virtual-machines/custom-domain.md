---
title: 사용자 지정 도메인 만들기 및 사용
description: Azure의 가상 머신에 사용자 지정 도메인을 연결합니다.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: 5bd9adb8354c3919a63e3de2e850513641af12a3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696551"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Azure VM 또는 리소스에 사용자 지정 도메인 추가

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합


Azure에서는 여러 가지 방법으로 사용자 지정 도메인을 VM 또는 리소스에 연결할 수 있습니다. 공용 IP를 사용하는 모든 리소스(Virtual Machine, Load Balancer, Application Gateway)에서 가장 간단한 방법은 해당 도메인 등록 기관에 A 레코드 집합을 만드는 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항 
- 웹 서버가 실행되는 VM이 필요합니다. [빠른 시작](./linux/quick-create-cli.md)을 사용하여 VM을 만들고 NGINX를 추가할 수 있습니다.

- VM은 웹에서 액세스할 수 있어야 합니다(포트 80 또는 443 열기). 보다 안전한 배포를 위해 부하 분산 장치 또는 Application Gateway 뒤에 먼저 VM을 배치합니다. 자세한 내용은 [빠른 시작: Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard)를 참조하세요.

- 기존 도메인 및 DNS 설정에 대한 액세스 권한이 있어야 합니다. 자세한 내용은 [Azure App Service의 사용자 지정 도메인 구입](../app-service/manage-custom-dns-buy-domain.md)을 참조하세요.


## <a name="add-custom-domain-to-vm-public-ip-address"></a>VM 공용 IP 주소에 사용자 지정 도메인 추가

Azure Portal에서 가상 머신을 만들면 가상 머신의 공용 IP 리소스가 자동으로 만들어집니다. 공용 IP 주소는 VM 개요 페이지에 표시됩니다. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="VM 개요 페이지의 기본 정보 섹션에 공용 IP 주소 표시":::

IP 주소를 선택하면 자세한 정보를 확인할 수 있습니다. **IP 할당** 이 **고정** 으로 설정되어 있는지 확인합니다. VM 또는 리소스가 다시 부팅되거나 종료되는 경우에도 고정 IP 주소는 변경되지 않습니다.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="IP 주소가 고정인지 확인할 수 있도록 공용 IP 구성 표시":::

IP 주소가 고정이 아닌 경우 FQDN을 만들어야 합니다. 

1. 포털에서 VM을 선택합니다. 
1. 왼쪽 메뉴에서 **속성** 을 선택합니다.
1. **공용 IP 주소/DNS 이름 레이블** 에서 IP 주소를 선택합니다.
2. **DNS 이름 레이블** 에서 사용할 접두사를 입력합니다.
3. 페이지 위쪽에서 **저장** 을 선택합니다.
4. 왼쪽 메뉴에서 **개요** 를 선택하여 VM 개요 블레이드로 돌아갑니다.
5. ‘DNS 이름’이 올바르게 표시되는지 확인합니다. 

브라우저를 열고 IP 주소 또는 FQDN을 입력한 다음, VM에서 실행되는 웹 콘텐츠가 표시되는지 확인합니다.
 
고정 IP 또는 FQDN을 확인한 후 도메인 공급자로 이동하여 DNS 설정을 탐색합니다.

여기에서 공용 IP 주소 또는 FQDN을 가리키는 ‘A 레코드’를 추가합니다. 예를 들어 GoDaddy 도메인 등록 기관에 대한 프로시저는 다음과 같습니다.
1. 로그인하고 사용하려는 사용자 지정 도메인을 선택합니다.
2. **도메인** 섹션에서 **모두 관리** 를 선택한 다음, **DNS | 영역 관리** 를 선택합니다.
3. **도메인 이름** 에서 사용자 지정 도메인을 입력한 다음, **검색** 을 선택합니다.
4. DNS 관리 페이지에서 추가를 선택하고 형식 목록에서 A를 선택합니다.
5. A 항목에 대한 필드를 입력합니다.
    - 형식: **A** 가 선택된 상태로 둡니다.
    - 호스트: **@** 을 입력합니다.
    - 가리키기: VM의 공용 IP 주소 또는 FQDN을 입력합니다. 
    - TTL: 1시간이 선택된 상태로 둡니다.
6. **저장** 을 선택합니다.

A 레코드 항목이 DNS 레코드 테이블에 추가됩니다.
 
레코드를 만든 후 DNS에서 전파하려면 일반적으로 1시간 정도 걸리지만, 경우에 따라 최대 48시간까지 걸릴 수 있습니다. 


 
## <a name="next-steps"></a>다음 단계
[Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](../application-gateway/ssl-overview.md)

