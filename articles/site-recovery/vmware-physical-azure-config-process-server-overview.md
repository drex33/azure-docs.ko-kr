---
title: Azure Site Recovery 구성/프로세스/마스터 대상 서버
description: 이 문서에서는 Azure Site Recovery를 사용하여 온-프레미스 VMware와 Azure 간 재해 복구를 설정할 때 사용되는 구성, 프로세스 및 마스터 대상 서버에 대한 개요를 제공합니다
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: ad8cdbdf9d051ac9b0e5a7fd47660811e4a285e3
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539431"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Site Recovery 구성 요소(구성,프로세스,마스터 대상) 정보

이 문서에서는 [Site Recovery](site-recovery-overview.md) 서비스에서 VMware VM 및 물리적 서버를 Azure에 복제하는데 사용되는 구성, 프로세스 및 마스터 대상 서버에 대해 설명합니다. 이 문서는 클래식 릴리스에 적용됩니다.

미리 보기에서 VM을 복제하려면 Azure Site Recovery 복제 서버를 만들어 사용해야 합니다. Azure Site Recovery 복제 서버 및 해당 구성 요소에 대한 자세한 내용은 [이 문서](vmware-azure-architecture-preview.md)를 참조하세요.

## <a name="configuration-server"></a>구성 서버

온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 위해 온-프레미스 Site Recovery 구성 서버를 배포합니다.

**설정** | **세부 정보** | **연결**
--- | --- | ---
**Components**  | 이 구성 서버 컴퓨터는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하는 모든 온-프레미스 Site Recovery 구성 요소를 실행합니다.<br/><br/> 구성 서버를 설정하면 모든 구성 요소가 자동으로 설치됩니다. | 구성 서버 FAQ [읽기](vmware-azure-common-questions.md#configuration-server).
**Role** | 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다. | Azure로의 [VMware](vmware-azure-architecture.md) 및 [물리적 서버](physical-azure-architecture.md) 재해 복구 아키텍처에 대해 자세히 알아보세요.
**VMware 요구 사항** | 온-프레미스 VMware VM의 재해 복구를 위해 구성 서버를 온-프레미스, 고 가용성 VMware VM으로 설치하고 실행해야 합니다. | 사전 요구 사항에 대해 [자세히 알아보세요](vmware-azure-deploy-configuration-server.md#prerequisites).
**VMware 배포** | 다운로드한 OVA 템플릿을 사용하여 구성 서버를 배포하는 것을 권장합니다. 이 방법은 모든 요구 사항 및 사전 요구 사항을 준수하는 구성 서버를 설정하는 간단한 방법을 제공합니다.<br/><br/> 어떤 이유로 OVA 템플릿을 이용한 VMware VM 배포가 불가능한 경우 아래의 물리적 컴퓨터 재해 복구에서 설명한 대로 구성 서버 컴퓨터를 수동으로 설정할 수 있습니다. | OVA 템플릿을 사용하여 [배포](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)합니다.
**물리적 서버 요구 사항** | 온-프레미스 물리적 서버의 재해 복구를 위해 구성 서버를 수동으로 배포합니다. | 사전 요구 사항에 대해 [자세히 알아보세요](physical-azure-set-up-source.md#prerequisites).
**실제 서버 배포** | VMware VM으로 설치할 수 없는 경우 물리적 서버에 설치할 수 있습니다. | 구성 서버를 수동으로 [배포](physical-azure-set-up-source.md#set-up-the-source-environment)합니다.

## <a name="process-server"></a>프로세스 서버

프로세스 서버는 장애 조치(failover) 및 장애 복구 중에 복제 데이터를 처리하고 온-프레미스 VMware VM 및 물리적 서버용 Mobility Service를 설치합니다.

**설정** | **세부 정보** | **연결**
--- | --- | ---
**배포**  | 기본적으로 구성 서버가 배포되면 프로세스 서버가 설치됩니다. <br/><br/> 온-프레미스 VMware VM 및 물리적 서버의 재해 복구 및 복제용 온-프레미스 프로세스 서버가 필요합니다. | [자세히 알아봅니다](vmware-azure-architecture.md#architectural-components).
**역할(온-프레미스**) | 복제를 사용하도록 설정된 컴퓨터에서 복제 데이터를 수신합니다. <br/><br/> 캐싱, 압축 및 암호화로 복제 데이터를 최적화하여 Azure Storage로 전송합니다. <br/><br/> 복제하려는 온-프레미스 VMware VM 및 물리적 서버에서 Site Recovery Mobility Service를 강제 설치합니다. <br/><br/> 온-프레미스 컴퓨터를 자동 검색합니다. | [자세히 알아봅니다](vmware-azure-enable-replication.md).
**역할(Azure로부터 장애 복구)** | 온-프레미스 사이트에서 장애 조치(failover) 후 Azure에서 프로세스 서버를 Azure VM으로 설정하여 온-프레미스 위치의 장애 복구를 처리합니다.<br/><br/> Azure의 프로세스 서버는 임시 서버입니다. 장애 복구가 완료된 후 Azure VM을 삭제할 수 있습니다. | [자세히 알아봅니다](vmware-azure-set-up-process-server-azure.md).
**크기 조정** | 대규모 배포의 경우 온-프레미스에서 추가 스케일 아웃 프로세스 서버를 설정할 수 있습니다. 추가 서버는 더 많은 복제 시스템과 더 많은 복제 트래픽을 처리하여 용량을 스케일 아웃합니다.<br/><br/> 복제 트래픽 부하를 분산하기 위해 두 프로세스 서버 간에 컴퓨터를 이동할 수 있습니다. | [자세히 알아봅니다](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>마스터 대상 서버

마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

- 기본적으로 마스터 대상 서버는 구성 서버에 설치됩니다.
- 대규모 배포의 경우 장애 복구를 위해 추가적인 별도의 마스터 대상 서버를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- VMware VM 및 물리적 서버의 재해 복구를 위한 [아키텍처](vmware-azure-architecture.md)를 검토합니다.
- Azure에 대한 VMware VM 및 물리적 서버의 재해 복구를 위한 [요구 사항 및 사전 요구 사항](vmware-physical-azure-support-matrix.md)을 검토합니다.
