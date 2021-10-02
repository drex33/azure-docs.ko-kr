---
title: 포함 파일
description: 포함 파일
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 17a98a50f4c6e6d0723b62f1ea402c78f653bf4c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378293"
---
| 리소스                                                                                                           | 제한                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 구독당 최대 클러스터 수                                                                                  | 5,000                                                                                                                                                                                                        |
| Virtual Machine Availability Sets 및 기본 Load Balancer SKU를 사용하는 클러스터당 최대 노드 수                       | 100                                                                                                                                                                                                         |
| Virtual Machine Scale Sets 및 [표준 Load Balancer SKU][standard-load-balancer]를 사용하는 클러스터당 최대 노드 수 | 1000(모든 [노드 풀][node-pool]에서)                                            |
| 클러스터당 최대 노드 풀                                                                                     | 100                                                                                  |
| 노드당 최대 Pod 수: Kubenet을 사용하는 [기본 네트워킹][basic-networking]                                           | 최대: 250 <br /> Azure CLI 기본값: 110 <br /> Azure Resource Manager 템플릿 기본값: 110 <br /> Azure Portal 배포 기본값: 30          |
| 노드당 최대 Pod 수: Azure Container Networking Interface를 사용하는 [고급 네트워킹][advanced-networking]        | 최대: 250 <br /> 기본값: 30                                                      |
| OSM(오픈 서비스 메시) AKS addon 미리 보기                                                                          | Kubernetes 클러스터 버전: 1.19+<sup>1</sup><br />클러스터당 OSM 컨트롤러: 1<sup>1</sup><br />OSM 컨트롤러당 Pod: 500<sup>1</sup><br />OSM에서 관리하는 Kubernetes 서비스 계정: 50<sup>1</sup> |

<sup>1</sup> AKS의 OSM 추가 기능은 미리 보기 상태 이며 GA (일반 공급) 이전에 추가 향상 된 기능을 제공 합니다. 미리 보기 단계에서 표시된 제한을 초과하지 않는 것이 좋습니다.<br />

<br />

| Kubernetes 제어 평면 계층 | 제한 |  
| -------------- | :--------------------------------------------- |
| 유료 계층      | 부하에 따라 자동으로 확장 됩니다.     |
| 무료 계층      | [처리 중인 요청 제한](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) 으로 제한 된 리소스 50 변경 및 100 읽기 전용 호출   |

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
