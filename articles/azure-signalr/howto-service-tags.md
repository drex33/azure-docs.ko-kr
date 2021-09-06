---
title: 서비스 태그 사용
titleSuffix: Azure SignalR Service
description: 서비스 태그를 사용하여 Azure SignalR Service로의 아웃바운드 트래픽 허용
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: fdb87dab5f2dc105da28ca097f81ff83c0dce3b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528681"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Azure SignalR Service에 대해 서비스 태그 사용

[네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#network-security-groups)을 구성할 때 Azure SignalR Service에 대해 [서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags)를 사용할 수 있습니다. 서비스 태그를 사용하면 IP 주소를 하드 코딩하지 않고도 Azure SignalR Service 엔드포인트에 대한 인바운드/아웃바운드 네트워크 보안 규칙을 정의할 수 있습니다.

Azure SignalR Service에서 해당 서비스 태그를 관리합니다. 사용자 고유의 서비스 태그를 만들거나 기존 태그를 수정할 수는 없습니다. Microsoft는 서비스 태그에 맞는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

> [!Note]
> 2021년 8월 15일부터 Azure SignalR Service는 인바운드 및 아웃바운드 트래픽 둘 다에 대해 양방향 서비스 태그를 지원합니다.

## <a name="use-service-tag-on-portal"></a>포털에서 서비스 태그 사용

### <a name="configure-outbound-traffic"></a>아웃바운드 트래픽 구성

새 아웃바운드 네트워크 보안 규칙을 추가하여 Azure SignalR Service로의 아웃바운드 트래픽을 허용할 수 있습니다.

1. 네트워크 보안 그룹으로 이동합니다.

1. **아웃바운드 보안 규칙** 이라는 설정 메뉴를 클릭합니다.

1. 맨 위에 있는 **+ 추가** 단추를 클릭합니다.

1. **대상** 에서 **서비스 태그** 를 선택합니다.

1. **대상 서비스 태그** 에서 **AzureSignalR** 을 선택합니다.

1. **대상 포트 범위** 에 **443** 을 입력합니다.

    ![아웃바운드 보안 규칙 만들기](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. 요구 사항에 따라 다른 필드를 조정합니다.

1. **추가** 를 클릭합니다.

### <a name="configure-inbound-traffic"></a>인바운드 트래픽 구성

업스트림이 있는 경우 새 인바운드 네트워크 보안 규칙을 추가하여 Azure SignalR Service의 인바운드 트래픽을 허용할 수도 있습니다.

1. 네트워크 보안 그룹으로 이동합니다.

1. **인바운드 보안 규칙** 이라는 설정 메뉴를 클릭합니다.

1. 맨 위에 있는 **+ 추가** 단추를 클릭합니다.

1. **원본** 에서 **서비스 태그** 를 선택합니다.

1. **원본 서비스 태그** 에서 **AzureSignalR** 을 선택합니다.

1. **\* *_를 _* 원본 포트 범위** 에 입력합니다.

   :::image type="content" alt-text="인바운드 보안 규칙 만들기" source="media/howto-service-tags/portal-add-inbound-security-rule.png" :::


1. 요구 사항에 따라 다른 필드를 조정합니다.

1. **추가** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [네트워크 보안 그룹: 서비스 태그](../virtual-network/network-security-groups-overview.md#security-rules)