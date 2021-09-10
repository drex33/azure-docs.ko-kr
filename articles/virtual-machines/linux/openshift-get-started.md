---
title: Azure의 OpenShift 개요
description: Azure의 OpenShift 개요입니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 940973aa0465c94e8df4882af9b2a1a89c65ff87
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687865"
---
# <a name="openshift-in-azure"></a>Azure의 OpenShift

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

OpenShift는 엔터프라이즈에 Docker 및 Kubernetes를 제공하는 확장 가능한 개방형 컨테이너 애플리케이션 플랫폼입니다.  

OpenShift는 컨테이너 오케스트레이션 및 관리를 위한 Kubernetes를 포함합니다. 추가로 다음을 지원하는 개발자 중심 및 작업 중심 도구를 제공합니다.

- RAD(신속한 애플리케이션 개발)
- 간편한 배포 및 확장
- 팀 및 애플리케이션에 대한 장기 수명 유지 관리

OpenShift의 여러 버전을 사용할 수 있습니다.  해당 버전 중 OpenShift Container Platform 및 OKD(이전의 OpenShift Origin)의 2가지만 고객이 Azure에서 배포할 수 있습니다.

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift는 Azure에서 실행되는 OpenShift의 완전 관리형 제품입니다. 이 서비스는 Microsoft 및 Red Hat에서 공동으로 관리 및 지원합니다. 자세한 내용은 [Azure Red Hat OpenShift 서비스](../../openshift/index.yml) 설명서를 참조하세요.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform은 Red Hat이 제공하고 지원하는 엔터프라이즈급 [상용](https://www.openshift.com) 버전입니다. 이 버전을 통해 고객은 OpenShift Container Platform에 필요한 자격을 구매하고 전체 인프라의 설치 및 관리를 담당합니다.

고객이 전체 플랫폼을 “소유”하기 때문에 온-프레미스 데이터 센터나 퍼블릭 클라우드(Azure 등)에 설치할 수 있습니다.

## <a name="okd"></a>OKD

OKD는 커뮤니티가 지원하는 OpenShift의 [오픈 소스](https://www.okd.io/) 업스트림 프로젝트입니다. OKD는 CentOS 또는 RHEL(Red Hat Enterprise Linux)에 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 OpenShift에 대한 일반적인 필수 조건 구성](./openshift-container-platform-3x-prerequisites.md)
- [Azure에서 OpenShift Container Platform 배포](./openshift-container-platform-3x.md)
- [OpenShift Container Platform 자체 관리형 Marketplace 제품 배포](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Azure Stack에 OpenShift 배포](./openshift-azure-stack.md)
- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
