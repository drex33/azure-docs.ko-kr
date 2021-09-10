---
title: Azure Kubernetes Service에 연결 - Azure Database for MySQL
description: Azure Database for MySQL과 Azure Kubernetes Service를 연결하는 방법에 대한 자세한 정보
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 9d40ea656f74df1083eadc276eea8a109abd44b0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294567"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Azure Kubernetes Service 및 Azure Database for MySQL에 대한 모범 사례

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

AKS(Azure Kubernetes Service)는 Azure에서 사용할 수 있는 관리형 Kubernetes 클러스터를 제공합니다. AKS 및 Azure Database for MySQL을 함께 사용하여 애플리케이션을 만드는 경우 고려할 몇 가지 옵션은 다음과 같습니다.

## <a name="create-database-before-creating-the-aks-cluster"></a>AKS 클러스터를 만들기 전에 데이터베이스 만들기

Azure Database for MySQL에는 두 가지 배포 옵션이 있습니다.

- 단일 서버
- 유연한 서버(미리 보기)

단일 서버는 단일 가용성 영역을 지원하고 유연한 서버는 여러 가용성 영역을 지원합니다. 반면에 AKS는 단일 또는 여러 가용성 영역을 사용하도록 설정하는 것도 지원합니다.  먼저 데이터베이스 서버를 만들어 서버가 있는 가용성 영역을 확인한 다음, 동일한 가용성 영역에 AKS 클러스터를 만듭니다. 이렇게 하면 네트워킹 대기 시간을 줄여 애플리케이션의 성능을 향상시킬 수 있습니다.

## <a name="use-accelerated-networking"></a>가속 네트워킹 사용

AKS 클러스터에서 가속 네트워킹이 활성화된 기본 VM을 사용합니다. VM에서 가속 네트워킹을 사용하도록 설정하면 VM의 대기 시간, 지터 및 CPU 사용률이 감소됩니다. 가속 네트워킹의 작동 방식, 지원되는 OS 버전 및 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)에 대해 지원되는 VM 인스턴스에 대해 자세히 알아봅니다.

2018년 11월부터 AKS는 지원되는 해당 VM 인스턴스에서 가속 네트워킹을 지원합니다. 이러한 VM을 사용하는 새 AKS 클러스터에서는 가속 네트워킹이 기본적으로 사용하도록 설정됩니다.

AKS 클러스터에 가속 네트워킹이 있는지 여부를 확인할 수 있습니다.

1. Azure Portal로 이동하고 AKS 클러스터를 선택합니다.
2. 속성 탭을 선택합니다.
3. **인프라 리소스 그룹** 의 이름을 복사합니다.
4. 포털 검색 표시줄을 사용하여 인프라 리소스 그룹을 엽니다.
5. 해당 리소스 그룹의 VM을 선택합니다.
6. VM의 **네트워킹** 탭으로 이동합니다.
7. **가속 네트워킹** 이 ‘사용’하도록 설정되었는지 확인합니다.

또는 Azure CLI를 통해 다음의 두 명령을 실행합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

이 명령의 출력은 네트워크 인터페이스를 포함하는 생성된 리소스 그룹(AKS에서 작성됨)입니다. "NodeResourceGroup" 이름을 가져와 다음 명령에서 사용합니다. **EnableAcceleratedNetworking** 은 true 또는 false가 됩니다.

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>Azure 프리미엄 파일 공유 사용

 하나 이상의 Pod에서 사용할 수 있고 동적 또는 정적으로 프로비전할 수 있는 영구 스토리지에 [Azure 프리미엄 파일 공유](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal)를 사용합니다. Azure 프리미엄 파일 공유는 파일 스토리지에서 많은 수의 I/O 작업이 예상되는 경우 애플리케이션에 최상의 성능을 제공합니다. 자세히 알아보려면 [Azure Files를 사용하도록 설정하는 방법](../aks/azure-files-dynamic-pv.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Kubernetes Service 클러스터 만들기](../aks/kubernetes-walkthrough.md)