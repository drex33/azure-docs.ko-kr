---
title: 리소스, SKU, 지역에 대한 한도
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)의 기본 할당량, 제한된 노드 VM SKU 크기, 지역 가용성에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 257598f1a1ea8bbc578fe44c40b8e2dafef36837
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266052"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성

모든 Azure 서비스는 특정 VM(가상 머신) SKU에 대한 사용 제한을 포함하여 리소스 및 기능에 대한 기본 한도 및 할당량을 설정합니다.

이 문서는 AKS(Azure Kubernetes Service)의 기본 리소스 한도와 Azure 지역의 Azure 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 컴퓨팅 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 한도는 [Azure 구독 및 서비스 한도](../azure-resource-manager/management/azure-subscription-service-limits.md)를 참조하세요.

> [!IMPORTANT]
> AKS 클러스터를 업그레이드하는 경우 추가 리소스가 일시적으로 사용됩니다. 이러한 리소스에는 가상 네트워크 서브넷 또는 가상 머신 vCPU 할당량에서 사용 가능한 IP 주소가 포함됩니다. 
>
> Windows Server 컨테이너의 경우 업그레이드 작업을 수행하여 최신 노드 업데이트를 적용할 수 있습니다. 이러한 임시 리소스 처리에 사용할 수 있는 IP 주소 공간 또는 vCPU 할당량이 없으면 클러스터 업그레이드 프로세스가 실패합니다. Windows Server 노드 업그레이드 프로세스에 대한 자세한 내용은 [AKS의 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

## <a name="supported-vm-sizes"></a>지원되는 VM 크기

AKS에서 지원되는 VM 크기 목록은 Azure에서 새 VM S SKU가 릴리스되면서 진화하고 있습니다. [AKS 릴리스 정보에](https://github.com/Azure/AKS/releases) 따라 지원되는 새로운 S SKU에 대한 정보를 확인하세요.

## <a name="restricted-vm-sizes"></a>제한된 VM 크기

CPU가 2개 미만인 VM 크기는 AKS에서 사용할 수 없습니다.

AKS 클러스터의 각 노드에는 vCPU 및 메모리 같은 고정된 양의 컴퓨팅 리소스가 포함되어 있습니다. AKS 노드에 컴퓨팅 리소스가 부족한 경우 Pod가 제대로 실행되지 않을 수 있습니다. 필요한 *kube-system Pod* 및 애플리케이션을 안정적으로 예약할 수 있도록 하려면 AKS에서 노드가 > 2개의 CPU가 있는 VM 크기를 사용해야 합니다.

VM 유형 및 해당 컴퓨팅 리소스에 대한 자세한 내용은 [Azure의 가상 머신 크기][vm-skus]를 참조하세요.

## <a name="region-availability"></a>지역 가용성

클러스터를 배포하고 실행할 수 있는 위치에 대한 최신 목록은 [AKS 지역 가용성][region-availability]을 참조하세요.

## <a name="cluster-configuration-presets-in-the-azure-portal"></a>Azure Portal의 클러스터 구성 미리 설정

Azure Portal을 사용하여 클러스터를 만드는 경우 미리 설정된 구성을 선택하여 시나리오에 따라 빠르게 사용자 지정할 수 있습니다. 언제든지 미리 설정된 값을 수정할 수 있습니다.

| 미리 설정           | 설명                                                            |
|------------------|------------------------------------------------------------------------|
| 표준         | 무엇을 선택해야 할지 잘 모르는 경우에 가장 적합합니다. 대부분의 애플리케이션에서 잘 작동합니다. |
| 개발/테스트         | AKS를 실험하거나 테스트 애플리케이션을 배포하는 데 가장 적합합니다. |
| 비용 최적화   | 중단을 허용할 수 있는 프로덕션 워크로드의 비용을 줄이는 데 가장 적합합니다. |
| 일괄 처리 | 기계 학습, 컴퓨팅 집약적 및 그래픽 집약적 워크로드에 가장 적합합니다. 클러스터의 빠른 확장 및 스케일 아웃이 필요한 애플리케이션에 적합합니다. |
| 강화된 액세스  | 보안 및 안정성을 완전히 제어해야 하는 대기업에 가장 적합합니다. |

## <a name="next-steps"></a>다음 단계

특정 기본 한도 및 할당량을 늘릴 수 있습니다. 리소스에서 증가를 지원하는 경우 [Azure 지원 요청][azure-support]을 통해 증가를 요청합니다(**문제 유형** 에 대해 **할당량** 선택).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
