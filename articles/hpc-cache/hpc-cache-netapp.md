---
title: Azure HPC Cache 및 Azure NetApp Files 사용
description: Azure HPC Cache를 사용하여 Azure NetApp Files를 통해 저장된 데이터에 대한 액세스를 개선하는 방법을 설명합니다.
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/05/2021
ms.author: femila
ms.openlocfilehash: 9a4e682b03ef93e0762dd8dc8117741329064caa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032927"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Azure NetApp Files와 함께 Azure HPC Cache 사용

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 Azure HPC Cache의 스토리지 대상으로 사용할 수 있습니다. 이 문서에서는 두 서비스를 함께 사용할 수 있는 방법을 설명하고 서비스 설정에 대한 팁을 제공합니다.

Azure NetApp Files에는 ONTAP 운영 체제와 Microsoft Azure의 확장성 및 속도가 결합되어 있습니다. 이 조합을 통해 사용자는 코드를 다시 작성하지 않고도 설정된 워크플로를 클라우드로 전환할 수 있습니다.

Azure HPC Cache 구성 요소를 추가하면 여러 Azure NetApp Files 볼륨을 하나의 집계된 네임스페이스로 제공하여 파일 액세스를 개선할 수 있습니다. 다른 서비스 지역에 있는 볼륨에 에지 캐싱을 제공할 수 있습니다. 하위 계층 서비스 수준에서 만들어진 볼륨에 대한 요청 시 성능을 높여 비용을 절감할 수도 있습니다.

## <a name="overview"></a>개요

Azure HPC Cache와 함께 Azure NetApp Files 시스템을 백 엔드 스토리지로 사용하려면 다음 프로세스를 수행합니다.

1. [아래에 있는 시스템 계획](#plan-your-azure-netapp-files-system)의 지침에 따라 Azure NetApp Files 시스템과 볼륨을 만듭니다.
1. 파일 액세스가 필요한 지역에 Azure HPC Cache를 만듭니다. ([Azure HPC Cache 만들기](hpc-cache-create.md)의 지침을 사용합니다.)
1. Azure NetApp Files 볼륨을 가리키는 캐시에 [스토리지 대상을 정의](#create-storage-targets-in-the-cache)합니다. 볼륨에 액세스하는 데 사용되는 고유한 IP 주소마다 하나의 캐시 스토리지 대상을 만듭니다.
1. 클라이언트가 Azure NetApp Files 볼륨을 직접 탑재하지 않고 [Azure HPC Cache를 탑재](#mount-storage-targets)하도록 합니다.

## <a name="plan-your-azure-netapp-files-system"></a>Azure NetApp Files 시스템 계획

Azure NetApp Files 시스템을 계획하는 경우 이 섹션의 항목에 유의하여 Azure HPC Cache와 원활하게 통합할 수 있는지 확인하세요.

또한 Azure HPC Cache와 함께 사용할 볼륨을 만들기 전에 먼저 [Azure NetApp Files 설명서](../azure-netapp-files/index.yml)를 읽어보세요.

### <a name="nfs-client-access-only"></a>NFS 클라이언트 액세스만 지원

Azure HPC Cache는 현재 NFS 액세스만 지원합니다. SMB ACL 또는 POSIX 모드 비트 볼륨에는 사용할 수 없습니다.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp Files 전용 서브넷

Azure NetApp Files에서는 해당 볼륨에 위임된 단일 서브넷을 사용합니다. 다른 리소스는 이 서브넷을 사용할 수 없습니다. 또한 가상 네트워크의 서브넷 하나만 Azure NetApp Files에 사용할 수 있습니다. [Azure NetApp Files 네트워크 계획 지침](../azure-netapp-files/azure-netapp-files-network-topologies.md)에서 자세히 알아보세요.

### <a name="delegated-subnet-size"></a>위임된 서브넷 크기

Azure HPC Cache와 함께 사용할 Azure NetApp Files 시스템을 만드는 경우 위임된 서브넷의 최소 크기를 사용합니다.

네트워크 마스크 /28을 통해 지정되는 최소 크기는 16개의 IP 주소를 제공합니다. 실제 Azure NetApp Files에서는 사용 가능한 IP 주소 중 세 개만 볼륨 액세스에 사용합니다. 즉, 모든 볼륨을 처리하는 데 세 개의 스토리지 대상만 Azure HPC Cache에 만들면 됩니다.

위임된 서브넷이 너무 크면 단일 Azure HPC Cache 인스턴스가 처리할 수 있는 것보다 더 많은 IP 주소를 Azure NetApp Files 볼륨에서 사용할 수 있습니다. 단일 캐시는 대부분의 캐시 처리량/스토리지 크기 조합에 대해 [10개의 스토리지 대상으로 제한](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)되거나 가장 큰 구성의 경우 20개의 스토리지 대상으로 제한됩니다.

Azure NetApp Files 설명서의 빠른 시작 예제에서는 위임된 서브넷에 10.7.0.0/16을 사용하여 너무 많은 서브넷을 제공합니다.

### <a name="capacity-pool-service-level"></a>용량 풀 서비스 수준

용량 풀의 [서비스 수준](../azure-netapp-files/azure-netapp-files-service-levels.md)을 선택하는 경우 워크플로를 고려합니다. 자주 Azure NetApp Files 볼륨에 데이터를 쓰기 저장하는 경우 쓰기 저장 시간이 오래 걸리면 캐시 성능이 제한될 수 있습니다. 쓰기를 자주 수행하는 볼륨에는 높은 서비스 수준을 선택하세요.

서비스 수준이 낮은 볼륨도 캐시에서 콘텐츠를 미리 채우는 동안에는 작업 시작 시 약간의 지연이 나타날 수 있습니다. 정상적으로 작동하는 파일 세트를 사용하여 캐시가 실행된 후에는 시간 지연이 나타나지 않아야 합니다.

용량 풀 서비스 수준을 만든 후에는 변경할 수 없으므로 미리 계획하는 것이 중요합니다. 새 볼륨은 다른 용량 풀에 만들고 데이터를 복사해야 합니다.

볼륨의 스토리지 할당량과 용량 풀의 크기는 액세스 중단 없이 변경할 수 있습니다.

## <a name="create-storage-targets-in-the-cache"></a>캐시에 스토리지 대상 만들기

Azure NetApp Files 시스템을 설정하고 Azure HPC Cache를 만든 후에는 파일 시스템 볼륨을 가리키는 캐시에 스토리지 대상을 정의합니다.

Azure NetApp Files 볼륨에서 사용하는 IP 주소마다 스토리지 대상을 하나씩 만듭니다. IP 주소는 볼륨의 탑재 지침 페이지에 나열되어 있습니다.

여러 볼륨에서 동일한 IP 주소를 공유하는 경우 모든 볼륨에 하나의 스토리지 대상을 사용할 수 있습니다.  

[Azure NetApp Files 설명서의 탑재 지침](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)에 따라 사용할 IP 주소를 찾습니다.

Azure CLI를 사용하여 IP 주소를 찾을 수도 있습니다.

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp Files 시스템의 내보내기 이름에는 단일 경로 구성 요소가 포함되어 있습니다. Azure NetApp Files에서 루트 내보내기 ``/``는 파일 액세스를 제공하지 않으므로 루트 내보내기의 스토리지 대상은 만들지 마세요.

스토리지 대상의 가상 네임스페이스 경로에 대한 특별한 제한은 없습니다.

## <a name="mount-storage-targets"></a>스토리지 대상 탑재

클라이언트 머신은 Azure NetApp Files 볼륨을 직접 탑재하지 않고 캐시를 탑재해야 합니다. [Azure HPC Cache 탑재](hpc-cache-mount.md)의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files](../azure-netapp-files/index.yml) 설정 및 사용에 대해 자세히 알아보세요.
* Azure NetApp Files를 사용하도록 Azure HPC Cache 시스템을 계획하고 설정하는 방법에 대한 도움말은 [고객 지원팀에 문의](hpc-cache-support-ticket.md)하세요.
