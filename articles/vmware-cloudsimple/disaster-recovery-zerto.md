---
title: Azure VMware Solution by CloudSimple - 온-프레미스 워크로드에 대한 재해 사이트로 프라이빗 클라우드 사용
description: CloudSimple 프라이빗 클라우드를 온-프레미스 VMware 워크로드에 대한 재해 복구 사이트로 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f9700b5d25a7d564c72bf959d48ad7cf1afce4e7
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612392"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>온-프레미스 VMware 워크로드를 사용하여 CloudSimple 프라이빗 클라우드를 재해 복구 사이트로 설정

CloudSimple 프라이빗 클라우드는 재해 발생 시 비즈니스 연속성을 제공하기 위해 온-프레미스 애플리케이션의 복구 사이트로 설정할 수 있습니다. 복구 솔루션은 복제 및 오케스트레이션 플랫폼인 Zerto Virtual Replication을 기반으로 합니다. 중요 인프라 및 애플리케이션 가상 머신은 온-프레미스 vCenter에서 프라이빗 클라우드로 지속적으로 복제할 수 있습니다. 장애 조치(failover) 테스트 및 재해 발생 시 애플리케이션의 가용성을 보장하기 위해 프라이빗 클라우드를 사용할 수 있습니다. 유사한 접근 방식을 따라 프라이빗 클라우드를 다른 위치의 복구 사이트로 보호되는 기본 사이트로 설정할 수 있습니다.

> [!NOTE]
> 재해 복구 환경 크기 조정에 대한 지침은 Zerto 문서 [Zerto Virtual Replication을 위한 크기 조정 고려 사항](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)을 참조하세요.

CloudSimple 솔루션:

* DR(재해 복구)을 위해 특별히 데이터 센터를 설정할 필요가 없습니다.
* 전 세계 지리적 복원력을 위해 CloudSimple이 배포되는 Azure 위치를 활용할 수 있습니다.
* DR에 대한 배포 비용과 총 소유 비용을 줄이는 옵션을 제공합니다.

이 솔루션을 사용하려면 다음이 필요합니다.

* 프라이빗 클라우드에서 Zerto를 설치, 구성 및 관리합니다.
* 프라이빗 클라우드가 보호되는 사이트인 경우 Zerto에 대한 자체 라이선스를 제공합니다. 라이선스를 위해 CloudSimple 사이트에서 실행되는 Zerto를 온-프레미스 사이트와 페어링할 수 있습니다.

다음 그림은 Zerto 솔루션의 아키텍처를 보여 줍니다.

![Architecture](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>솔루션 배포 방법

다음 섹션에서는 프라이빗 클라우드에서 Zerto Virtual Replication을 사용하여 DR 솔루션을 배포하는 방법을 설명합니다.

1. [전제 조건](#prerequisites)
2. [CloudSimple 프라이빗 클라우드의 선택적 구성](#optional-configuration-on-your-private-cloud)
3. [CloudSimple 프라이빗 클라우드에서 ZVM 및 VRA 설정](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Zerto 가상 보호 그룹 설정](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>사전 요구 사항

온-프레미스 환경에서 프라이빗 클라우드로 Zerto Virtual Replication을 사용하도록 설정하려면 다음 필수 조건을 완료합니다.

1. [온-프레미스 네트워크와 CloudSimple 프라이빗 클라우드 간에 사이트 간 VPN 연결을 설정합니다](set-up-vpn.md).
2. [프라이빗 클라우드 관리 구성 요소가 프라이빗 클라우드 DNS 서버로 전달되도록 DNS 조회를 설정합니다](on-premises-dns-setup.md).  DNS 조회의 전달을 사용하도록 설정하려면 CloudSimple DNS 서버에 대한 `*.cloudsimple.io`의 온-프레미스 DNS 서버에 전달 영역 항목을 만듭니다.
3. 온-프레미스 vCenter 구성 요소가 온-프레미스 DNS 서버로 전달되도록 DNS 조회를 설정합니다.  DNS 서버는 사이트 간 VPN을 통해 CloudSimple 프라이빗 클라우드에서 연결할 수 있어야 합니다. 도움이 필요하면 다음 정보를 제공하여 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출하세요.  

    * 온-프레미스 DNS 도메인 이름
    * 온-프레미스 DNS 서버 IP 주소

4. 프라이빗 클라우드에 Windows 서버를 설치합니다. 서버는 Zerto Virtual Manager를 설치하는 데 사용됩니다.
5. [CloudSimple 권한을 에스컬레이션합니다](escalate-private-cloud-privileges.md).
6. Zerto Virtual Manager의 서비스 계정으로 사용할 관리 역할을 사용하여 프라이빗 클라우드 vCenter에 새 사용자를 만듭니다.

### <a name="optional-configuration-on-your-private-cloud"></a>프라이빗 클라우드의 선택적 구성

1. 프라이빗 클라우드 vCenter에 하나 이상의 리소스 풀을 만들어 온-프레미스 환경에서 VM에 대한 대상 리소스 풀로 사용합니다.
2. 프라이빗 클라우드 vCenter에 하나 이상의 폴더를 만들어 온-프레미스 환경에서 VM에 대한 대상 리소스 풀로 사용합니다.
3. 장애 조치(failover) 네트워크용 VLAN을 만들고 방화벽 규칙을 설정합니다. 도움이 필요하면 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 엽니다.
4. 장애 조치(failover) 네트워크를 위한 분산 포트 그룹을 만들고 VM의 장애 조치(failover)를 테스트하기 위해 네트워크를 테스트합니다.
5. [DHCP 및 DNS 서버](dns-dhcp-setup.md)를 설치하거나 프라이빗 클라우드 환경에서 Active Directory 도메인 컨트롤러를 사용합니다.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>프라이빗 클라우드에서 ZVM 및 VRA 설정

1. 프라이빗 클라우드의 Windows Server에 ZVM(Zerto Virtual Manager)을 설치합니다.
2. 이전 단계에서 만든 서비스 계정을 사용하여 ZVM에 로그인합니다.
3. Zerto Virtual Manager에 대한 라이선스를 설정합니다.
4. 프라이빗 클라우드의 ESXi 호스트에 Zerto VRA(Virtual Replication Appliance)를 설치합니다.
5. 프라이빗 클라우드 ZVM을 온-프레미스 ZVM과 페어링합니다.

### <a name="set-up-zerto-virtual-protection-group"></a>Zerto 가상 보호 그룹 설정

1. 새 VPG(가상 보호 그룹)를 만들고 VPG의 우선 순위를 지정합니다.
2. 비즈니스 연속성을 위해 보호가 필요한 가상 머신을 선택하고 필요한 경우 부팅 순서를 사용자 지정합니다.
3. 복구 사이트를 프라이빗 클라우드로 선택하고 기본 복구 서버를 프라이빗 클라우드 클러스터 또는 만든 리소스 그룹으로 선택합니다. 프라이빗 클라우드의 복구 데이터 저장소에 대해 **vsanDatastore** 를 선택합니다.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > VM 설정 옵션에서 개별 VM에 대한 호스트 옵션을 사용자 지정할 수 있습니다.

4. 필요에 따라 스토리지 옵션을 사용자 지정합니다.
5. 이전에 만든 분산 포트 그룹으로 장애 조치(failover) 네트워크 및 장애 조치(failover) 테스트 네트워크에 사용할 복구 네트워크를 지정하고 필요에 따라 복구 스크립트를 사용자 지정합니다.
6. 필요한 경우 개별 VM에 대한 네트워크 설정을 사용자 지정하고 VPG를 만듭니다.
7. 복제가 완료되면 장애 조치(failover)를 테스트합니다.

## <a name="reference"></a>참조

[Zerto 문서](https://www.zerto.com/myzerto/technical-documentation/)
