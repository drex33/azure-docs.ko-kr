---
title: Azure Stack 허브에 OpenShift 배포
description: Azure Stack Hub에 OpenShift를 배포 합니다.
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/19/2021
ms.openlocfilehash: a506b6f7a8ddc11c075bb115bf23e7c7b0497973
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871417"
---
# <a name="deploy-openshift-container-platform-or-okd-to-azure-stack-hub"></a>Azure Stack Hub에 OpenShift Container Platform 또는 OKD 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

[Openshift](openshift-get-started.md) 는 Azure Stack 허브에 배포할 수 있습니다. Azure와 Azure Stack 허브 간에는 몇 가지 중요 한 차이점이 있습니다. 따라서 배포는 약간 다르고 기능이 약간 다를 수도 있습니다.

현재 Azure 클라우드 공급자는 Azure Stack 허브에서 작동 하지 않습니다. Azure Stack Hub의 영구 저장소에는 디스크 연결을 사용할 수 없습니다. 대신 NFS, iSCSI, GlusterFS 등의 다른 저장소 옵션을 구성할 수 있습니다. 또는 CN을 사용 하도록 설정 하 고 영구 저장소에 GlusterFS를 사용할 수 있습니다. CN을 사용 하는 경우 GlusterFS 사용을 위한 저장소를 사용 하 여 세 개 이상의 노드가 배포 됩니다.

## <a name="deploy-openshift-3x-on-azure-stack-hub"></a>Azure Stack 허브에 OpenShift 3(sp3) 배포

여러 가지 방법 중 하나를 사용 하 여 Azure Stack 허브에 OpenShift Container Platform 또는 OKD를 배포할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음 [OpenShift Container Platform 설명서](https://docs.openshift.com/container-platform) 또는 [OKD 설명서](https://docs.okd.io)를 따르는 방법이 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화 하는 기존 [Azure Resource Manager 템플릿을](https://github.com/Microsoft/openshift-container-platform/) 사용할 수도 있습니다.
- OKD 클러스터의 배포를 간소화 하는 기존 [Azure Resource Manager 템플릿을](https://github.com/Microsoft/openshift-origin) 사용할 수도 있습니다.

Azure Resource Manager 템플릿을 사용 하는 경우 적절 한 분기 (azurestack-릴리스인-2.x)를 선택 합니다. Azure와 Azure Stack 허브 간에 API 버전이 다르기 때문에 Azure 용 템플릿이 작동 하지 않습니다. RHEL 이미지 참조는 현재 azuredeploy.json 파일에 하드 코딩되어 있으므로 이미지에 맞게 변경해야 합니다.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 또는 정품 인증 키, 조직 ID 및 풀 ID를 사용할 수 있습니다.  에서이 정보를 확인할 수 있습니다 https://access.redhat.com .

### <a name="azure-stack-hub-prerequisites"></a>Azure Stack 허브 필수 조건

OpenShift 클러스터를 배포 하려면 RHEL 이미지 (OpenShift Container Platform) 또는 OKD (CentOS image)를 Azure Stack 허브 환경에 추가 해야 합니다. 이러한 이미지를 추가 하려면 Azure Stack 허브 클라우드 운영자에 게 문의 하세요. 지침은 다음에서 찾을 수 있습니다.

- [Azure Stack 허브에 사용자 지정 VM 이미지 추가 및 제거](/azure-stack/operator/azure-stack-add-vm-image)
- [Azure Stack Hub에 사용할 수 있는 Azure Marketplace 항목](/azure-stack/operator/azure-stack-marketplace-azure-items)
- [Azure Stack 허브에 대 한 Red Hat 기반 가상 머신 제공](/azure-stack/operator/azure-stack-redhat-create-upload-vhd)

### <a name="deploy-by-using-the-openshift-container-platform-or-okd-azure-resource-manager-template"></a>OpenShift Container Platform 또는 OKD Azure Resource Manager 템플릿을 사용 하 여 배포

Azure Resource Manager 템플릿을 사용 하 여 배포 하려면 매개 변수 파일을 사용 하 여 입력 매개 변수를 제공 합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기 (의 변수 `azuredeploy.json` )
- 명명 규칙 (의 변수 ` azuredeploy.json` )
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨 ( `deployOpenShift.sh` )
- RHEL 이미지 참조 (의 변수 `azuredeploy.json` )

Azure CLI를 사용하는 배포 단계에 대해서는 [OpenShift Container Platform](./openshift-container-platform-3x.md) 섹션 또는 [OKD](./openshift-okd.md) 섹션 중에서 해당하는 섹션을 따르세요.
## <a name="deploy-openshift-4x-on-azure-stack-hub"></a>Azure Stack 허브에 OpenShift 4.x 배포

Red Hat는 openshift 4.x의 RHCOS (Red Hat Enterprise Linux CoreOS) 이미지를 관리 합니다. 배포 프로세스는 Red Hat 끝점에서 이미지를 가져옵니다. 따라서 사용자 (테 넌 트)는 Azure Stack 허브 Marketplace에서 이미지를 가져올 필요가 없습니다.

[ARM 템플릿을 사용 하 여 Azure Stack 허브에 클러스터 설치](https://docs.openshift.com/container-platform/4.9/installing/installing_azure_stack_hub/installing-azure-stack-hub-user-infra.html)에서 열린 Shift 설명서의 단계를 따를 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)