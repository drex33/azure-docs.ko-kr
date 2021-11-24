---
title: SAP 배포 자동화 프레임워크에 대한 지원 가능성 매트릭스
description: Azure의 SAP 배포 자동화 프레임워크에 지원되는 플랫폼, 토폴로지 및 기능
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 5ff62fe4908e4ad2d83d1ad6d7044a675d14e6de
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031962"
---
# <a name="supportability-matrix-for-the-sap-automation-framework"></a>SAP Automation Framework에 대한 지원 가능성 매트릭스

[Azure의 SAP 배포 자동화 프레임워크는](automation-deployment-framework.md) 지원되는 모든 Azure의 SAP 토폴로지의 배포를 지원합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

### <a name="control-plane"></a>제어 평면

Ansible 컨트롤러는 Linux에서만 작동하기 때문에 컨트롤 플레인의 배포자 가상 머신을 Linux에 배포해야 합니다.

### <a name="sap-application"></a>SAP 애플리케이션

자동화 프레임워크는 x86-64 또는 x64 하드웨어의 Linux 또는 Windows 가상 머신 모두에서 Azure의 SAP 인프라 배포를 지원합니다.   

프레임워크에서 지원되는 운영 체제 및 배포는 다음과 같습니다.

- x86-64 플랫폼용 Windows server 64비트
- x86-64 플랫폼용 SUSE linux 64비트(12.x 및 15.x)
- x86-64 플랫폼용 Red Hat Linux 64비트(7.x 및 8.x)
- x86-64 플랫폼용 Oracle Linux 64비트

프레임워크(Red Hat 7.9, Red Hat 8.2, SUSE 12 SP5 및 SUSE 15 SP2)를 통해 다음 배포를 테스트했습니다.
## <a name="supported-topologies"></a>지원되는 토폴로지

기본적으로 자동화 프레임워크는 데이터베이스 및 애플리케이션 계층을 통해 배포됩니다. 애플리케이션 계층은 애플리케이션, 중앙 서비스 및 웹 디스패처의 세 가지 계층으로 분할됩니다. 

애플리케이션 계층 없이 구성을 지정하여 자동화 프레임워크를 독립 실행형 서버에 배포할 수도 있습니다.

## <a name="supported-deployment-topologies"></a>지원되는 배포 토폴로지

자동화 프레임워크는 녹색 필드와 브라운 필드 배포를 모두 지원합니다. 

### <a name="greenfield-deployments"></a>Greenfield 배포
녹색 필드 배포에서는 자동화 프레임워크에서 필요한 모든 리소스를 생성합니다.

이 시나리오에서는 환경을 구성할 때 관련 데이터(네트워크 및 서브넷의 주소 공간)를 제공합니다. 자세한 [예제는 워크로드 영역 구성을](automation-configure-workload-zone.md) 참조하세요.

### <a name="brownfield-deployments"></a>Brownfield 배포
브라운필드 배포에서는 기존 Azure 리소스를 배포의 일부로 사용할 수 있습니다.

이 시나리오에서는 환경을 구성할 때 기존 리소스에 대한 Azure 리소스 식별자를 제공합니다. 자세한 [예제는 워크로드 영역 구성을](automation-configure-workload-zone.md) 참조하세요.

## <a name="supported-azure-features"></a>지원되는 Azure 기능

자동화 프레임워크는 다음 Azure 서비스, 기능 및 기능을 사용하거나 사용할 수 있습니다.

- Azure VM(Virtual Machines)
    - 가속 네트워킹
    - 앵커 VM(선택 사항)
    - SSH 인증
    - 사용자 이름 및 암호 인증
    - SKU 구성
    - 사용자 지정 이미지
    - 신규 또는 기존 근접 배치 그룹
- Azure Virtual Network(VNet)
    - SAP 네트워크에 피어된 네트워크의 배포
    - 고객이 지정한 IP 주소 지정
    - Azure에서 제공하는 IP 주소 지정
    - 신규 또는 기존 네트워크 보안 그룹
    - 신규 또는 기존 가상 네트워크
    - 신규 또는 기존 서브넷
- Azure 가용성 영역
    - HA(고가용성)
- Azure Firewall
- Azure Load Balancer
    - 표준 부하 분산 장치
- Azure Storage
    - 부팅 진단 스토리지
    - SAP 설치 미디어 스토리지
    - Terraform 상태 파일 스토리지
    - HA 시나리오에 대한 클라우드 감시 스토리지
- Azure Key Vault
    - 신규 또는 기존 키 자격 증명 모음
    - 디스크 암호화를 위한 고객 관리형 키
- ASG(Azure 애플리케이션 보안 그룹)
- ANF(Azure NetApp Files)
    - 공유 파일의 경우

## <a name="unsupported-azure-features"></a>지원되지 않는 Azure 기능

현재 자동화 프레임워크는 다음과 같은 Azure 서비스, 기능 또는 기능을 **지원하지 않습니다.**

- NFS용 Azure Files
- ANF(Azure NetApp Files)
    - 데이터베이스 파일의 경우

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> [자동화 프레임워크 시작](automation-get-started.md)
