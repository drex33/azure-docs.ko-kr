---
title: Azure Virtual Network Manager의 보안 관리자 규칙(미리 보기)
description: Azure Virtual Network Manager의 보안 관리자 규칙에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: d533ae6860850830640f052f4737b61c199d952e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851777"
---
# <a name="security-admin-rules-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager의 보안 관리자 규칙(미리 보기)

Azure Virtual Network Manager는 가상 네트워크에서 배포할 수 있는 두 가지 유형의 구성을 제공합니다. 그 중 하나는 *SecurityAdmin* 구성입니다. 보안 관리자 구성에는 규칙 컬렉션 집합이 포함되어 있습니다. 각 규칙 컬렉션에는 하나 이상의 보안 관리자 규칙이 포함됩니다. 그런 다음, 보안 관리자 규칙을 적용하려는 네트워크 그룹과 규칙 컬렉션을 연결합니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="security-admin-rules"></a>보안 관리자 규칙

보안 관리자 규칙을 사용하면 조건 집합과 일치하는 보안 정책 조건을 적용할 수 있습니다. Azure Virtual Network Manager 인스턴스의 범위 내에서만 리소스에 대한 보안 관리 규칙을 정의할 수 있습니다. 이러한 보안 규칙은 NSG(네트워크 보안 그룹) 규칙보다 우선 순위가 높으며 NSG 규칙 전에 평가됩니다. 또한 보안 관리자 규칙은 NSG 규칙을 변경하지 않습니다. 아래 그림을 참조하세요.

:::image type="content" source="./media/concept-security-admins/traffic-evaluation.png" alt-text="보안 관리자 규칙 및 NSG를 통해 트래픽을 평가하는 방법에 대한 다이어그램입니다.":::

보안 관리자 규칙을 사용하여 보안 규칙을 적용할 수 있습니다. 예를 들어 관리자는 보안 관리자 규칙을 사용하여 인터넷의 모든 높은 위험 포트 또는 프로토콜을 거부할 수 있습니다. 이러한 보안 관리자 규칙은 모든 NSG 규칙 전에 평가되기 때문입니다.

> [!IMPORTANT]
> 일부 서비스에는 네트워크 트래픽이 해당 서비스에 필요한 대로 작동하는지 확인하는 네트워크 의도 정책이 있습니다. 보안 관리자 규칙을 사용하는 경우 해당 서비스에 대해 만든 네트워크 의도 정책을 중단시킬 수 있습니다. 예를 들어 거부 관리자 규칙을 만들면 SQL *관리되는 인스턴스* 서비스에서 허용하는 일부 트래픽을 차단할 수 있으며, 이 트래픽은 네트워크 의도 정책에 의해 정의됩니다. 보안 관리자 구성을 적용하기 전에 환경을 검토해야 합니다. 자세한 내용은 [거부 규칙을 갖기 전에 SQLMI 트래픽을 명시적으로 허용하는 방법을 참조하세요.](faq.md#how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules)

다음은 보안 관리자 규칙에서 정의할 수 있는 필드입니다.

## <a name="required-fields"></a>Required fields

### <a name="priority"></a>우선 순위

보안 규칙 우선 순위는 0에서 99 사이의 정수에 의해 결정됩니다. 값이 낮을수록 규칙의 우선 순위가 높습니다. 예를 들어 우선 순위가 10인 거부 규칙은 우선 순위가 20인 허용 규칙을 재정의합니다. 

### <a name="action"></a><a name = "action"></a>작업

보안 규칙에 대한 세 가지 작업 중 하나를 정의할 수 있습니다.

* **허용:** 지정된 방향으로 특정 포트, 프로토콜 및 원본/대상 IP 접두사에 대한 트래픽을 허용합니다.
* **거부:** 지정된 방향으로 지정된 포트, 프로토콜 및 원본/대상 IP 접두사에서 트래픽을 차단합니다.
* **항상 허용:** 우선 순위가 낮은 다른 규칙이나 사용자 정의 NSG에 관계없이 지정된 포트, 프로토콜 및 원본/대상 IP 접두사에서 지정된 방향으로 트래픽을 허용합니다.

### <a name="direction"></a>방향

규칙이 적용되는 트래픽 방향을 지정할 수 있습니다. 인바운드 또는 아웃바운드를 정의할 수 있습니다.

### <a name="protocol"></a>프로토콜

현재 보안 관리자 규칙에서 지원되는 프로토콜은 다음과 같습니다.

* TCP
* UDP
* ICMP
* ESP
* 아
* 모든 프로토콜

## <a name="optional-fields"></a>선택적 필드

### <a name="source-and-destination-types"></a>원본 및 대상 형식

* **IP 주소:** CIDR 표기에서 IPv4 또는 IPv6 주소 또는 주소 블록을 제공할 수 있습니다. 여러 IP 주소를 나열하려면 각 IP 주소를 쉼표로 구분합니다.
* **서비스 태그:** 지역 또는 전체 서비스에 따라 특정 서비스 태그를 정의할 수 있습니다. 지원되는 태그 목록은 [사용 가능한 서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)를 참조하세요.

### <a name="source-and-destination-ports"></a>원본 및 대상 포트

원본 또는 대상에서 차단할 특정 공용 포트를 정의할 수 있습니다. 일반적인 TCP 포트 목록은 아래를 참조하세요.

| 포트 | 서비스 이름 |
| ----- | ------------ |
| 20, 21 | FTP |
| 22 | SSH |
| 23 | 텔넷 |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

## <a name="next-steps"></a>다음 단계 

[SecurityAdmin 구성을](how-to-block-network-traffic-portal.md)사용하여 네트워크 트래픽을 차단하는 방법을 알아봅니다.
