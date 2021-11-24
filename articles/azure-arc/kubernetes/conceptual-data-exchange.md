---
title: Azure Arc 지원 Kubernetes 클러스터와 Azure 간에 교환되는 데이터
services: azure-arc
ms.service: azure-arc
ms.date: 11/23/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 지원 Kubernetes 클러스터와 Azure 간에 교환되는 데이터에 대한 정보를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 0d9512b4147e31364226484329d44b214669818c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133077513"
---
# <a name="data-exchanged-between-azure-arc-enabled-kubernetes-cluster-and-azure"></a>Azure Arc 지원 Kubernetes 클러스터와 Azure 간에 교환되는 데이터

Azure Arc 지원 Kubernetes에서 사용하도록 설정된 시나리오에는 원하는 상태 구성, 메타데이터 및 Azure Arc 지원 Kubernetes 클러스터 환경과 Azure 서비스 간의 기타 시나리오 관련 운영 데이터 교환이 포함됩니다. 모든 유형의 데이터에 대해 Azure Arc 에이전트는 Azure 서비스에 대한 아웃바운드 통신을 시작하므로 네트워크 필수 조건 아래에 나열된 엔드포인트에 대한 송신 액세스만 필요합니다. Azure Arc 에이전트에는 방화벽에서 인바운드 포트를 사용하도록 설정할 필요가 없습니다.

다음 표에서는 이러한 환경 간에 교환된 데이터의 시나리오별 분석을 제공합니다.

## <a name="data-exchange-between-cluster-and-azure"></a>클러스터와 Azure 간의 데이터 교환

| 시나리오 | 메타데이터 | 통신 모드 |
| --------- | -------- | ------------------ |
| 클러스터 메타데이터 | Kubernetes 클러스터 버전 | Azure에 대한 에이전트 밀어넣기 |
| 클러스터 메타데이터 | 클러스터의 총 노드 수 | Azure에 대한 에이전트 밀어넣기 |
| 클러스터 메타데이터 | 에이전트 버전 | Azure에 대한 에이전트 밀어넣기 |
| 클러스터 메타데이터 | Kubernetes 배포 유형 | Azure에 대한 Azure CLI 밀어넣기 |
| 클러스터 메타데이터 | 인프라 형식(AWS/GCP/vSphere/...) | Azure에 대한 Azure CLI 밀어넣기 |
| 클러스터 메타데이터 | 클러스터에 있는 노드의 vCPU 수 | Azure에 대한 에이전트 밀어넣기 |
| 리소스 상태 | 에이전트 하트비트 | Azure에 대한 에이전트 밀어넣기 |
| 진단 및 지원 가능성 | 에이전트별 리소스 사용량(메모리/CPU) | Azure에 대한 에이전트 밀어넣기 |
| 진단 및 지원 가능성 | 모든 에이전트 컨테이너의 로그 | Azure에 대한 에이전트 밀어넣기 |
| 에이전트 업그레이드 | 에이전트 업그레이드 가용성 | Azure에서 에이전트 끌어오기 |
| 구성(GitOps) | 필요한 구성 상태: Git 리포지토리 URL, flux 연산자 매개 변수, 프라이빗 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름, 토큰 또는 암호 | Azure에서 에이전트 끌어오기 |
| 구성(GitOps) | Flux 운영자 설치 상태 | Azure에 대한 에이전트 밀어넣기 |
| 확장 | 원하는 확장 상태: 확장 유형, 구성 설정, 보호된 구성 설정, 릴리스 학습, 자동 업그레이드 설정 | Azure에서 에이전트 끌어오기 |
| Azure Policy | 클러스터 내에서 Gatekeeper를 적용해야 하는 Azure Policy 할당 | Azure에서 에이전트 끌어오기 |
| Azure Policy | 클러스터 내 정책 적용의 감사 및 준수 상태 | Azure에 대한 에이전트 밀어넣기 |
| Azure Monitor | 고객 워크로드의 메트릭 및 로그 | 고객의 테넌트 및 구독에서 Log Analytics 작업 영역 리소스에 대한 에이전트 밀어넣기 |
| 클러스터 커넥트 | 클러스터로 전송된 요청 | 클러스터에 요청을 보내는 데 사용되는 clusterconnect-agent에 의해 Arc 서비스로 설정된 아웃바운드 세션 |
| 사용자 지정 위치 | 네임스페이스의 메타데이터 및 권한 부여를 위한 ClusterRoleBinding/RoleBinding | 클러스터에 요청을 보내는 데 사용되는 clusterconnect-agent에 의해 Arc 서비스로 설정된 아웃바운드 세션 |
| 사용자 지정 위치 위에 있는 리소스 | 데이터베이스 또는 애플리케이션 인스턴스의 원하는 사양 | 클러스터에 요청을 보내는 데 사용되는 clusterconnect-agent에 의해 Arc 서비스로 설정된 아웃바운드 세션 |

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* [Azure Arc 지원 Kubernetes를](./conceptual-configurations.md)사용하여 구성 리소스로 클러스터와 Git 리포지토리 간의 연결을 만드는 방법에 대해 자세히 알아봅니다.


