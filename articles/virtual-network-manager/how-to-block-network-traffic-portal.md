---
title: Azure Virtual Network Manager (미리 보기)를 사용 하 여 네트워크 트래픽을 차단 하는 방법-Azure Portal
description: Azure Portal에서 Azure Virtual Network Manager의 보안 규칙을 사용 하 여 네트워크 트래픽을 차단 하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 20adb2a748f839d63122a1fc5904746a29af33ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103621"
---
# <a name="how-to-block-network-traffic-with-azure-virtual-network-manager-preview---azure-portal"></a>Azure Virtual Network Manager (미리 보기)를 사용 하 여 네트워크 트래픽을 차단 하는 방법-Azure Portal

이 문서에서는 규칙 컬렉션에 추가할 수 있는 RDP 포트 3389에서 인바운드 네트워크 트래픽을 차단 하는 보안 관리자 규칙을 만드는 방법을 보여 줍니다. 자세한 내용은 [보안 관리 규칙](concept-security-admins.md)을 참조 하세요.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

보안 관리 규칙 구성을 시작 하기 전에 다음 단계를 수행 했는지 확인 합니다.

* [보안 관리 규칙](concept-security-admins.md)의 각 요소를 이해 합니다.
* [Azure Virtual Network Manager 인스턴스](create-virtual-network-manager-portal.md)를 만들었습니다.

## <a name="create-a-securityadmin-configuration"></a>SecurityAdmin 구성 만들기

1. *설정* 아래에서 **구성** 을 선택 하 고 **+ 구성 추가를** 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-admin.png" alt-text="보안 관리 구성 추가의 스크린샷":::

1. 드롭다운 메뉴에서 **SecurityAdmin** 를 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-admin-drop-down.png" alt-text="구성 추가 드롭다운의 스크린샷":::

1. 이 보안 구성을 식별 하는 *이름을* 입력 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/security-configuration-name.png" alt-text="보안 구성 이름 필드의 스크린샷":::

## <a name="add-a-rule-collection"></a>규칙 컬렉션 추가

1. **+ 규칙 컬렉션 추가** 를 선택 하 여 규칙 집합을 만듭니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-collection-button.png" alt-text="규칙 컬렉션 추가 단추의 스크린샷":::

1. 이 규칙 컬렉션을 식별 하는 *이름을* 입력 하 고 규칙 집합을 적용할 *네트워크 그룹* 을 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/rule-collection-target.png" alt-text="규칙 컬렉션 이름 및 대상 네트워크 그룹의 스크린샷":::

## <a name="add-a-security-rule"></a>보안 규칙 추가

1. *규칙 컬렉션 추가 페이지* 에서 **+ 규칙 추가** 를 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule-button.png" alt-text="규칙 추가 단추의 스크린샷":::

1. 다음 정보를 입력 하거나 선택한 다음, **추가** 를 선택 하 여 규칙 컬렉션에 규칙을 추가 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/add-rule.png" alt-text="규칙 추가 페이지의 스크린샷":::

    | 설정 | 값 |
    | ------- | ----- |
    | 속성 | 규칙 이름에 **Deny_RDP** 이름을 입력 합니다. |
    | 설명 | 규칙에 대 한 설명을 입력 합니다. |
    | Priority | 0에서 99 사이의 값을 입력 하 여 규칙의 우선 순위를 결정 합니다. 값이 낮을수록 우선 순위가 높습니다. 이 예에서는 **1** 을 입력 합니다.|
    | 조치 | 트래픽을 차단 하려면 **거부** 를 선택 합니다. 자세한 내용은 [작업](concept-security-admins.md#action) 을 참조 하세요.
    | 방향도 | 이 규칙을 사용 하 여 인바운드 트래픽을 거부 하려는 경우 **인바운드** 를 선택 합니다. |
    | 프로토콜만 | **TCP** 프로토콜을 선택 합니다. HTTP 및 HTTPS는 TCP 포트입니다. |
    | 소스 형식 | **IP 주소** 또는 **서비스 태그** 의 원본 유형을 선택 합니다. |
    | 원본 IP 주소 | 이 필드는 *IP 주소의* 원본 유형을 선택할 때 표시 됩니다. CIDR 표기법을 사용 하 여 IPv4 또는 IPv6 주소 또는 범위를 입력 하세요. 쉼표를 사용 하 여 두 개 이상의 주소 또는 주소 블록을 개별적으로 정의 하는 경우 이 예에서는 비워 둡니다.|
    | 원본 서비스 태그 | *서비스 태그* 의 원본 유형을 선택 하면이 필드가 표시 됩니다. 원본으로 지정 하려는 서비스에 대 한 서비스 태그를 선택 합니다. 지원 되는 태그 목록은 [사용 가능한 서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)를 참조 하세요. |
    | 원본 포트 | 단일 포트 번호 또는 포트 범위 (예: (1024-65535))를 입력 합니다. 둘 이상의 포트 또는 포트 범위를 정의할 때는 쉼표를 사용 하 여 구분 합니다. 포트를 지정 하려면 *를 입력 합니다. 이 예에서는 비워 둡니다.|
    | 대상 형식 | **IP 주소** 또는 **서비스 태그** 의 대상 유형을 선택 합니다. |
    | 대상 IP 주소 | 이 필드는 *IP 주소의* 대상 유형을 선택할 때 표시 됩니다. CIDR 표기법을 사용 하 여 IPv4 또는 IPv6 주소 또는 범위를 입력 하세요. 쉼표를 사용 하 여 두 개 이상의 주소 또는 주소 블록을 개별적으로 정의 하는 경우 |
    | 대상 서비스 태그 | 이 필드는 *서비스 태그* 의 대상 유형을 선택 하면 표시 됩니다. 대상으로 지정 하려는 서비스에 대 한 서비스 태그를 선택 합니다. 지원 되는 태그 목록은 [사용 가능한 서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)를 참조 하세요. |
    | 대상 포트 | 단일 포트 번호 또는 포트 범위 (예: (1024-65535))를 입력 합니다. 둘 이상의 포트 또는 포트 범위를 정의할 때는 쉼표를 사용 하 여 구분 합니다. 포트를 지정 하려면 *를 입력 합니다. 이 예의 경우 **3389** 을 입력 합니다. |

1. 규칙 컬렉션에 규칙을 더 추가 하려면 1-3 단계를 다시 반복 합니다.

1. 만들려는 모든 규칙에 만족 했으면 **저장** 을 선택 하 여 규칙 컬렉션을 보안 관리자 구성에 추가 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/save-rule-collection.png" alt-text="규칙 컬렉션에 있는 규칙의 스크린샷":::

1. 그런 다음 **추가** 를 선택 하 여 구성을 만듭니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/create-security-configuration.png" alt-text="보안 구성을 만들기 위한 추가 단추의 스크린샷":::

## <a name="deploy-the-security-admin-configuration"></a>보안 관리 구성 배포

새 보안 관리자 구성을 만들었으면 네트워크 그룹의 가상 네트워크에 적용할 수 있도록이 구성을 배포 해야 합니다.

1. *설정* 아래에서 **배포** 를 선택한 다음 **구성 배포** 를 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-configuration.png" alt-text="구성 배포 단추의 스크린샷":::

1. **SecurityAdmin** 구성 유형을 선택 하 고, 마지막 섹션에서 만든 구성을 선택 합니다. 그런 다음이 구성을 배포할 지역을 선택 하 고 **배포** 를 선택 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/deploy-security-configuration.png" alt-text="보안 구성 배포 페이지의 스크린샷":::

1. **확인** 을 선택 하 여 기존 구성을 덮어쓸지 확인 하 고 보안 관리 구성을 배포 합니다.

    :::image type="content" source="./media/how-to-block-network-traffic-portal/confirm-security.png" alt-text="보안 구성 배포에 대 한 확인 메시지 스크린샷":::

## <a name="update-existing-security-admin-configuration"></a>기존 보안 관리자 구성 업데이트

* 업데이트 하는 보안 관리자 구성이 정적 구성원을 포함 하는 네트워크 그룹에 적용 되는 경우 구성을 다시 배포 해야 적용 됩니다.
* 보안 관리자 구성은 네트워크 그룹의 동적 구성원에 게 자동으로 적용 됩니다.

## <a name="verify-security-admin-rules"></a>보안 관리자 규칙 확인

보안 관리 규칙을 적용 한 가상 네트워크 중 하나에서 가상 머신에 대 한 **네트워킹** 설정으로 이동 합니다. 가상 네트워크 중 하나에 테스트 가상 머신을 배포 하지 않은 경우 가상 네트워크 중 하나에 배포 합니다. 이제 Azure Virtual Network Manager에서 적용 하는 보안 규칙에 대 한 네트워크 보안 그룹 규칙 아래에 새 섹션이 표시 됩니다.

:::image type="content" source="./media/how-to-block-network-traffic-portal/vm-security-rules.png" alt-text="가상 컴퓨터 네트워크 설정에서 보안 관리자 규칙의 스크린샷" lightbox="./media/how-to-block-network-traffic-portal/vm-security-rules-expanded.png":::

## <a name="next-steps"></a>다음 단계

[보안 관리 규칙](concept-security-admins.md)에 대해 자세히 알아보세요.
