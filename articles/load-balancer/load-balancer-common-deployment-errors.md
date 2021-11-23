---
title: 일반 배포 오류 문제 해결
titleSuffix: Azure Load Balancer
description: Azure Load Balancer를 배포할 때 발생하는 일반적인 오류를 해결하는 방법을 설명합니다.
services: load-balancer
documentationcenter: na
tags: top-support-issue
author: anavinahar
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 11/22/2021
ms.author: anavin
ms.openlocfilehash: 058baee6623d10e1888d33700928cc3f9c5f50de
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934217"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-load-balancer"></a>Azure Load Balancer를 사용하여 일반적인 Azure 배포 오류 해결

이 문서에서는 일반적인 Azure Load Balancer 배포 오류 중 일부에 대해 설명하고 오류를 해결하기 위한 정보를 제공합니다. 오류 코드에 대한 정보를 찾고 있는데 이 문서에서 해당 정보를 제공하지 않는다면 알려주세요. 이 페이지의 하단에서 사용자 의견을 남길 수 있습니다. 사용자 의견은 GitHub 문제를 통해 추적됩니다.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 세부 정보 및 완화 방법 |
| ------- | ---------- |
|DifferentSkuLoadBalancersAndPublicIPAddressNotAllowed| 공용 IP SKU와 Load Balancer SKU가 일치해야 합니다. Azure Load Balancer와 공용 IP SKU가 일치하는지 확인하세요. 표준 SKU는 프로덕션 워크로드용으로 권장됩니다. [SKU의 차이점](./skus.md)에 대해 자세히 알아보기  |
|DifferentSkuLoadBalancerAndPublicIPAddressNotAllowedInVMSS | 가상 머신 확장 집합은 SKU가 지정되지 않았거나 표준 공용 IP 없이 배포된 경우 기본적으로 기본 Load Balancer로 설정됩니다. 개별 인스턴스에 표준 공용 IP를 사용하여 가상 머신 확장 집합을 다시 배포하는 방식으로 표준 Load Balancer가 선택되었는지 확인하거나 Azure Portal에서 가상 머신 확장 집합을 배포할 때 표준 LB를 선택하기만 하면 됩니다. |
|MaxAvailabilitySetsInLoadBalancerReached | Load Balancer의 백 엔드 풀에는 최대 150개의 가용성 집합이 포함될 수 있습니다. 백 엔드 풀의 VM에 명시적으로 정의된 가용성 집합이 없는 경우 각 단일 VM은 자체 가용성 집합으로 이동합니다. 따라서 150개의 독립 실행형 VM을 배포한다면 150개의 가용성 집합이 있는 것이므로 한도에 도달하게 됩니다. 가용성 집합을 배포하고 이에 대한 VM을 추가하면 이 문제를 해결할 수 있습니다. |
|NetworkInterfaceAndLoadBalancerAreInDifferentAvailabilitySets | 기본 SKU 부하 분산 장치의 경우 네트워크 인터페이스와 부하 분산 장치는 동일한 가용성 집합에 있어야 합니다. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndIPConfig| 동일한 가상 머신 확장 집합에서 참조하는 동일한 백 엔드 포트 및 프로토콜을 사용하여 지정된 부하 분산 장치 유형(내부, 공용)에는 둘 이상의 규칙을 적용할 수 없습니다. 규칙을 업데이트하여 이 중복 규칙 생성을 변경하세요. |
|RulesOfSameLoadBalancerTypeUseSameBackendPortProtocolAndVmssIPConfig| 동일한 가상 머신 확장 집합에서 참조하는 동일한 백 엔드 포트 및 프로토콜을 사용하여 지정된 부하 분산 장치 유형(내부, 공용)에는 둘 이상의 규칙을 적용할 수 없습니다. 규칙 매개 변수를 업데이트하여 이 중복 규칙 생성을 변경하세요. |
|AnotherInternalLoadBalancerExists| Load Balancer의 백 엔드에서 동일한 VM/네트워크 인터페이스 세트를 참조하는 내부 유형 Load Balancer는 하나만 존재할 수 있습니다. 동일한 유형의 Load Balancer를 하나만 만들도록 배포를 업데이트하세요. |
|CannotUseInactiveHealthProbe| 가상 머신 확장 집합 상태에 대해 구성된 규칙에서 사용하지 않는 프로브는 사용할 수 없습니다. 설정된 프로브를 적극적으로 사용 중인지 확인하세요. |
|VMScaleSetCannotUseMultipleLoadBalancersOfSameType| 동일한 유형(내부, 공용)의 Load Balancer를 여러 개 사용할 수 없습니다. 내부 및 공용 Load Balancer는 최대 1개만 사용할 수 있습니다. |
|VMScaleSetCannotReferenceLoadbalancerWhenLargeScaleOrCrossAZ | 다중 배치 그룹 가상 머신 확장 집합 또는 교차 가용성 영역 가상 머신 확장 집합에 대한 기본 Load Balancer는 지원되지 않습니다. 표준 Load Balancer를 대신 사용하세요. |
|MarketplacePurchaseEligibilityFailed | 구독이 EA 구독으로 전환됨에 따라 구매가 가능하도록 올바른 관리 계정으로 전환하세요. 자세한 내용은 [여기](../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-)를 참조하세요. |
|ResourceDeploymentFailure| 부하 분산 장치에 장애가 발생한 경우 다음 단계에 따라 복구하세요.<ol><li>https://resources.azure.com 으로 이동하고 Azure Portal 자격 증명으로 로그인합니다.</li><li>**읽기/쓰기** 를 선택합니다.</li><li>왼쪽에서 **구독** 을 확장한 다음, 업데이트할 Load Balancer로 구독을 확장합니다.</li><li>**ResourceGroups** 를 확장한 다음, 업데이트할 Load Balancer로 리소스 그룹을 확장합니다.</li><li>**Microsoft.Network** > **LoadBalancers** 를 선택한 다음, 업데이트할 Load Balancer로 **LoadBalancer_1** 을 선택합니다.</li><li>**LoadBalancer_1** 에 대한 표시 페이지에서 **가져오기** > **편집** 을 선택합니다.</li><li>**ProvisioningState** 값을 **실패** 에서 **성공** 으로 업데이트합니다.</li><li>**PUT** 을 선택합니다.</li></ol>|
|LoadBalancerWithoutFrontendIPCantHaveChildResources | 프런트 엔드 IP 구성이 없는 Load Balancer 리소스에는 연결된 자식 리소스 또는 연결된 구성 요소가 있을 수 없습니다. 이 오류를 완화하려면 프런트 엔드 IP 구성을 추가한 다음 추가하려는 리소스를 추가합니다. |
| LoadBalancerRuleCountLimitReachedForNic | 백 엔드 풀 멤버의 네트워크 인터페이스(가상 머신, 가상 머신 확장 집합)는 300개 이상의 규칙에 연결될 수 없습니다. 규칙 수를 줄이거나 다른 Load Balancer 활용합니다. 이 제한은 Load Balancer [제한 페이지에 설명되어 있습니다.](https://aka.ms/lblimits)

## <a name="next-steps"></a>다음 단계

* Azure Load Balancer [SKU 비교 표](./skus.md) 살펴보기
* [Azure Load Balancer 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer) 알아보기
