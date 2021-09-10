---
title: Azure Site Recovery의 VMware 재해 복구 구성 서버 요구 사항.
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware 재해 복구를 위해 구성 서버를 배포할 때 지원 및 요구 사항에 대해 설명합니다.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: 71861ec8f3b1e10f0052fe46705b726f06e717dd
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531074"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Azure로 VMware 재해 복구를 위한 구성 서버 요구 사항

>[!NOTE]
> 이 문서의 정보는 Azure Site Recovery 클래식 릴리스에 적용됩니다. 미리 보기에서 VM을 복제하려면 Azure Site Recovery 복제 어플라이언스를 만들어 사용해야 합니다. 복제 어플라이언스의 요구 사항 및 배포 방법에 대한 자세한 내용은 [이 문서를 참조](deploy-vmware-azure-replication-appliance-preview.md)하세요.

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 배포합니다.

- 구성 서버는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다.
- 구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

## <a name="configuration-server-deployment"></a>구성 서버 배포

VMware VM을 Azure로 재해 복구하기 위해 구성 서버를 VMware VM으로 배포합니다.

- Site Recovery는 사용자가 Azure Portal에서 다운로드하고 vCenter Server로 가져와서 구성 서버 VM을 설정하는 OVA 템플릿을 제공합니다.
- OVA 템플릿을 사용하여 구성 서버를 배포하면 VM이 이 문서에 나열된 요구 사항을 자동으로 준수합니다.
- OVA 템플릿을 사용하여 구성 서버를 설정하는 것이 좋습니다. 그러나 VMware VM에 대한 재해 복구를 설정 중이고 OVA 템플릿을 사용할 수 없는 경우에는 [제공된 지침](physical-azure-set-up-source.md)을 사용하여 구성 서버를 배포할 수 있습니다.
- 온-프레미스 물리적 머신을 Azure로 재해 복구하기 위한 구성 서버를 배포하는 경우 [이 문서](physical-azure-set-up-source.md)의 지침을 따르세요.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>다음 단계
Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
