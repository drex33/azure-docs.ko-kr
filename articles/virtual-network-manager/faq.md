---
title: Azure Virtual Network Manager에 대 한 질문과 대답
description: Azure Virtual Network Manager에 대해 자주 묻는 질문에 대 한 답변을 찾습니다.
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 921c829cefae8ab4ea96066d70c5d110fc9188f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451942"
---
# <a name="azure-virtual-network-manager-faq"></a>Azure Virtual Network 관리자 FAQ

## <a name="general"></a>일반

### <a name="what-regions-are-available-in-public-preview"></a>공개 미리 보기에서 사용할 수 있는 지역은 무엇 인가요?

* 미국 중북부

* 미국 서부

* 미국 서부 2

* 미국 동부

* 미국 동부 2

* 북유럽

* 서유럽

* 프랑스 중부

### <a name="what-are-common-use-cases-for-using-azure-virtual-network-manager"></a>Azure Virtual Network Manager를 사용 하는 일반적인 사용 사례는 무엇 인가요?

* IT 보안 관리자는 환경 및 해당 기능에 대 한 요구 사항을 충족 하는 다른 네트워크 그룹을 만들 수 있습니다. 예를 들어 프로덕션 환경 및 테스트 네트워크 환경, 개발 팀, 재무 부서 등을 위한 네트워크 그룹을 만들어 대규모로 연결 및 보안 규칙을 관리할 수 있습니다. 

* 연결 구성을 적용 하 여 조직의 구독에서 많은 수의 가상 네트워크에 대 한 메시 또는 허브 및 스포크 네트워크 토폴로지를 만들 수 있습니다. 

* 높은 위험 수준의 트래픽을 거부할 수 있습니다. 엔터프라이즈 관리자는 일반적으로 트래픽을 허용 하는 NSG 규칙을 재정의 하는 특정 프로토콜 또는 원본을 차단할 수 있습니다.   

* 항상 트래픽 허용: 트래픽을 거부 하도록 구성 된 NSG 규칙이 있더라도 특정 보안 스캐너가 항상 모든 리소스에 대 한 인바운드 연결을 허용 하도록 허용 합니다.   

## <a name="technical"></a>기술적

### <a name="can-a-virtual-network-belong-to-multiple-azure-virtual-network-managers"></a>가상 네트워크가 여러 Azure Virtual Network 관리자에 속할 수 있나요?

예, 가상 네트워크는 둘 이상의 Azure Virtual Network Manager에 속할 수 있습니다.

### <a name="what-is-a-global-mesh-network-topology"></a>전역 메시 네트워크 토폴로지 란?

전역 메시를 사용 하면 서로 다른 지역에 있는 가상 네트워크에서 서로 통신할 수 있습니다. 효과는 글로벌 가상 네트워크 피어 링의 작동 방식과 비슷합니다.

### <a name="is-there-a-limit-to-how-many-network-groups-can-be-created"></a>만들 수 있는 네트워크 그룹 수에 제한이 있나요?

만들 수 있는 네트워크 그룹 수에는 제한이 없습니다.

### <a name="how-do-i-remove-the-deployment-of-all-applied-configurations"></a>적용 된 모든 구성의 배포를 제거 어떻게 할까요??

구성이 적용 된 모든 지역에 **None** 구성을 배포 해야 합니다.

### <a name="can-i-add-virtual-networks-from-another-subscription-not-managed-by-myself"></a>자신에 의해 관리 되지 않는 다른 구독에서 가상 네트워크를 추가할 수 있나요?

예, 해당 가상 네트워크에 액세스할 수 있는 적절 한 권한이 있어야 합니다.

### <a name="what-is-dynamic-group-membership"></a>동적 그룹 멤버 자격 이란?

자세한 내용은 [동적 멤버 자격](concept-network-groups.md#dynamic-membership)을 참조 하세요.

### <a name="how-does-the-deployment-of-configuration-differ-for-dynamic-membership-and-static-membership"></a>동적 멤버 자격 및 정적 멤버 자격에 대 한 구성 배포는 어떻게 다릅니까?

자세한 내용은 [멤버 자격 유형에 대 한 배포](concept-deployments.md#deployment)를 참조 하세요.

### <a name="how-do-i-delete-an-azure-virtual-network-manager-component"></a>Azure Virtual Network Manager 구성 요소를 삭제 어떻게 할까요??

자세한 내용은 [구성 요소 제거 검사 목록](concept-remove-components-checklist.md)을 참조 하세요.

### <a name="how-can-i-see-what-configurations-are-applied-to-help-me-troubleshoot"></a>문제 해결에 도움이 되는 구성을 어떻게 확인할 수 있나요?

Virtual network에 대 한 **네트워크 관리자** 에서 Azure Virtual Network Manager 설정을 볼 수 있습니다. 적용 되는 연결 및 보안 관리 구성을 모두 볼 수 있습니다. 자세한 내용은 [적용 된 구성 보기](how-to-view-applied-configurations.md)를 참조 하세요.

### <a name="can-a-virtual-network-managed-by-azure-virtual-network-manager-be-peered-to-a-non-managed-virtual-network"></a>Azure Virtual Network Manager를 통해 관리 되는 가상 네트워크를 관리 되지 않는 가상 네트워크로 피어 링 수 있나요?

예, 이미 만든 기존 피어 링을 재정의 또는 삭제 하도록 선택할 수 있습니다.

### <a name="how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules"></a>거부 규칙을 사용 하기 전에 명시적으로 SQLMI 트래픽을 허용 하려면 어떻게 해야 하나요?

Azure SQL Managed Instance에는 몇 가지 네트워크 요구 사항이 있습니다. 보안 관리자 규칙이 네트워크 요구 사항을 차단할 수 있는 경우 아래 샘플 규칙을 사용 하 여 SQL Managed Instance 트래픽을 차단할 수 있는 거부 규칙 보다 우선 순위가 높은 sqlmi 트래픽을 허용할 수 있습니다.

#### <a name="inbound-rules"></a>인바운드 규칙

| 포트 | 프로토콜 | 원본 | 대상 | 작업 |
| ---- | -------- | ------ | ----------- | ------ |
| 9000, 9003, 1438, 1440, 1452 | TCP | SqlManagement | **VirtualNetwork** | 허용 |
| 9000, 9003 | TCP | CorpnetSaw | **VirtualNetwork** | 허용 |
| 9000, 9003 | TCP | CorpnetPublic | **VirtualNetwork** | Allow |
| 모두 | 모두 | **VirtualNetwork** | **VirtualNetwork** | Allow |
| 모두 | 모두 | **AzureLoadBalancer** | **VirtualNetwork** | 허용 |

#### <a name="outbound-rules"></a>아웃바운드 규칙

| 포트 | 프로토콜 | 원본 | 대상 | 작업 |
| ---- | -------- | ------ | ----------- | ------ |
| 443, 12000 | TCP  | **VirtualNetwork** | AzureCloud | 허용 |
| 모두 | 모두 | **VirtualNetwork** | **VirtualNetwork** | 허용 |

## <a name="limits"></a>제한

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>Azure Virtual Network Manager의 서비스 제한은 무엇 인가요?

* 허브 및 스포크 토폴로지의 허브는 최대 250 스포크로 피어 링 수 있습니다. 

* 메시 토폴로지에서는 최대 250 개의 가상 네트워크를 사용할 수 있습니다.

* 가상 네트워크의 서브넷은 메시 구성에 동일한 주소 공간이 있는 경우 서로 통신할 수 없습니다. 

* 결합 된 모든 관리자 규칙의 최대 IP 접두사 수는 1000입니다. 

* Azure Virtual Network Manager의 한 수준에서 최대 관리자 규칙 수는 100입니다. 

* Azure Virtual Network Manager는 공개 미리 보기에서 교차 테 넌 트를 지원 하지 않습니다.

* 가상 네트워크는 최대 두 개의 메시 구성에 포함 될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure Portal을 사용하여 [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
