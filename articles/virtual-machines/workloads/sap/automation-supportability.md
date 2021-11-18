---
title: SAP deployment automation 프레임 워크에 대 한 지원 가능성 매트릭스
description: Azure의 SAP deployment automation 프레임 워크에 대해 지원 되는 플랫폼, 토폴로지 및 기능.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: b9295fdffc215d842bf53f1a7dcfbf984b764a07
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132730439"
---
# <a name="supportability-matrix-for-the-sap-automation-framework"></a>SAP Automation 프레임 워크에 대 한 지원 가능성 매트릭스

[Azure의 sap 배포 자동화 프레임 워크](automation-deployment-framework.md) 는 azure 토폴로지에서 지원 되는 모든 SAP의 배포를 지원 합니다.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

### <a name="control-plane"></a>제어 평면

Ansible 컨트롤러가 Linux 에서만 작동 하므로 제어 평면의 배포자 가상 머신은 Linux에 배포 되어야 합니다.

### <a name="sap-application"></a>SAP 응용 프로그램

자동화 프레임 워크는 Linux 또는 Windows 가상 컴퓨터의 Azure 인프라에서 SAP 배포를 지원 합니다. 

## <a name="supported-topologies"></a>지원되는 토폴로지

기본적으로 자동화 프레임 워크는 데이터베이스 및 응용 프로그램 계층과 함께 배포 됩니다. 응용 프로그램 계층은 3 개의 계층, 즉 응용 프로그램, 중앙 서비스 및 웹 디스패처로 분할 됩니다. 

응용 프로그램 계층 없이 구성을 지정 하 여 독립 실행형 서버에 automation 프레임 워크를 배포할 수도 있습니다.

## <a name="supported-deployment-topologies"></a>지원 되는 배포 토폴로지

자동화 프레임 워크는 녹색 필드 및 갈색 필드 배포를 모두 지원 합니다. 

### <a name="greenfield-deployments"></a>최적의 배포
녹색 필드 배포에서 필요한 모든 리소스는 자동화 프레임 워크에 의해 만들어집니다.

이 시나리오에서는 환경을 구성할 때 관련 데이터 (네트워크 및 서브넷에 대 한 주소 공간)를 제공 합니다. 더 많은 예제 [는 작업 영역 구성](automation-configure-workload-zone.md) 을 참조 하세요.

### <a name="brownfield-deployments"></a>Brownfield 배포
Brownfield 배포에서는 기존 Azure 리소스를 배포의 일부로 사용할 수 있습니다.

이 시나리오에서는 환경을 구성할 때 기존 리소스에 대 한 Azure 리소스 식별자를 제공 합니다. 더 많은 예제 [는 작업 영역 구성](automation-configure-workload-zone.md) 을 참조 하세요.

## <a name="supported-azure-features"></a>지원 되는 Azure 기능

자동화 프레임 워크는 또는를 사용 하 여 다음과 같은 Azure 서비스, 기능 및 기능을 사용할 수 있습니다.

- Azure VM(Virtual Machines)
    - 가속 네트워킹
    - Vm 고정 (선택 사항)
    - SSH 인증
    - 사용자 이름 및 암호 인증
    - SKU 구성
    - 사용자 지정 이미지
    - 신규 또는 기존 근접 배치 그룹
- Azure Virtual Network (VNet)
    - SAP 네트워크에 피어 링 네트워크에서 배포
    - 고객이 지정한 IP 주소 지정
    - Azure에서 제공 하는 IP 주소 지정
    - 신규 또는 기존 네트워크 보안 그룹
    - 새 가상 네트워크 또는 기존 가상 네트워크
    - 신규 또는 기존 서브넷
- Azure 가용성 영역
    - HA(고가용성)
- Azure Firewall
- Azure Load Balancer
    - 표준 부하 분산 장치
- Azure Storage
    - 부트 진단 저장소
    - SAP 설치 미디어 저장소
    - Terraform 상태 파일 저장소
    - HA 시나리오용 클라우드 감시 저장소
- Azure Key Vault
    - 새 키 자격 증명 모음 또는 기존 키 자격 증명 모음
    - 디스크 암호화에 대 한 고객 관리 키
- ASG (Azure 애플리케이션 보안 그룹)
- Azure NetApp Files (ANF)
    - 공유 파일의 경우

## <a name="unsupported-azure-features"></a>지원 되지 않는 Azure 기능

이번에는 자동화 프레임 워크에서 다음과 같은 Azure 서비스, 기능 또는 기능을 **지원 하지 않습니다** .

- NFS 용 Azure Files
- Azure NetApp Files (ANF)
    - 데이터베이스 파일의 경우

## <a name="next-steps"></a>다음 단계


> [!div class="nextstepaction"]
> [자동화 프레임 워크 시작](automation-get-started.md)
