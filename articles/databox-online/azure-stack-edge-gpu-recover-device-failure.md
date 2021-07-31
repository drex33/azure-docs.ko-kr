---
title: Azure Stack Edge Pro 디바이스 오류에서 복구
description: Azure Stack Edge Pro 실패한 디바이스에서 복구하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636643"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>실패한 Azure Stack Edge Pro GPU 디바이스에서 복구 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스의 허용할 수 없는 실패에서 복구하는 방법을 설명합니다. Azure Stack Edge Pro GPU 디바이스의 허용할 수 없는 실패에는 디바이스 교체가 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에

아래와 같은 파일과 버전이 있어야 합니다.

- 디바이스 실패에 관해 Microsoft 지원에 문의했고 Microsoft 지원은 디바이스 교체를 추천했습니다. 
- [디바이스 실패 대비](azure-stack-edge-gpu-prepare-device-failure.md)에 설명된 대로 디바이스 구성을 백업했습니다.


## <a name="configure-replacement-device"></a>교체 디바이스 구성

디바이스에서 허용할 수 없는 실패가 발생하면 교체 디바이스를 주문해야 합니다. 교체 디바이스의 구성 단계는 동일하게 유지됩니다. 

실패한 디바이스에서 백업한 디바이스 구성 정보를 검색합니다. 이 정보를 사용하여 교체 디바이스를 구성합니다.  

다음 단계에 따라 교체 디바이스를 구성합니다.

1. [배포 검사 목록](azure-stack-edge-gpu-deploy-checklist.md)에 필요한 정보를 수집합니다. 이전 디바이스 구성에서 저장한 정보를 사용할 수 있습니다. 
1. 실패한 디바이스와 동일한 구성의 새 디바이스를 주문합니다.  주문을 넣으려면 Azure Portal에서 [새 Azure Stack Edge 리소스를 만듭니다](azure-stack-edge-gpu-deploy-prep.md#).
1. [제품을 개봉](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)하고, [랙을 탑재](azure-stack-edge-gpu-deploy-install.md#rack-the-device)하고, [디바이스 케이블을 연결](azure-stack-edge-gpu-deploy-install.md#cable-the-device)합니다. 
1. [디바이스의 로컬 UI에 연결](azure-stack-edge-gpu-deploy-connect.md)합니다.
1. 이전 디바이스에 사용한 동일한 IP 주소를 사용하여 네트워크를 구성합니다. 동일한 IP 주소를 사용하면 환경에서 사용되는 모든 클라이언트 머신에 미치는 영향이 최소화됩니다. [네트워크 설정 구성](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md) 방법을 참조하세요.
1. 이전 디바이스와 동일한 디바이스 이름 및 DNS 도메인을 할당합니다. 이렇게 하면 클라이언트는 동일한 디바이스 이름을 사용하여 새 디바이스와 통신할 수 있습니다. [디바이스 설정 구성](azure-stack-edge-gpu-deploy-set-up-device-update-time.md) 방법을 참조하세요.
1. 이전 디바이스와 동일한 방식으로 새 디바이스에서 인증서를 구성합니다. 새 디바이스에는 새 노드 일련 번호가 있습니다. 이전 디바이스에서 고유한 인증서를 사용한 경우 새 노드 인증서를 가져와야 합니다. [인증서 구성](azure-stack-edge-gpu-deploy-configure-certificates.md) 방법을 참조하세요.
1. Azure Portal에서 활성화 키를 가져오고 새 디바이스를 활성화합니다. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md) 방법을 참조하세요.

이제 이전 디바이스에서 실행 중이던 워크로드를 배포할 준비가 되었습니다.

## <a name="restore-edge-cloud-shares"></a>Edge 클라우드 공유 복원

다음 단계에 따라 디바이스에서 Edge 클라우드 공유의 데이터를 복원합니다.

1. 이전에 실패한 디바이스에서 만든 것과 동일한 공유 이름을 가진 [공유를 추가](azure-stack-edge-gpu-manage-shares.md#add-a-share)합니다. 공유를 만드는 동안 **Blob 컨테이너 선택** 옵션이 **기존 항목 사용** 으로 설정되었는지 확인한 다음, 이전 디바이스에서 사용된 컨테이너를 선택합니다.
1. 이전 디바이스에 대한 액세스 권한이 있는 [사용자를 추가](azure-stack-edge-gpu-manage-users.md#add-a-user)합니다.
1. 이전에 디바이스에서 공유와 연결된 [스토리지 계정을 추가](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account)합니다. Edge 스토리지 계정을 만드는 동안 기존 컨테이너에서 선택하고 이전 디바이스에서 매핑된 Azure Storage 계정에 매핑된 컨테이너를 가리킵니다. 이전 디바이스의 Edge 스토리지 계정에 기록된 디바이스의 데이터는 매핑된 Azure Storage 계정에서 선택한 스토리지 컨테이너에 자동으로 업로드되었습니다.
1. Azure에서 공유 [데이터를 새로 고칩니다](azure-stack-edge-gpu-manage-shares.md#refresh-shares). 그러면 기존 컨테이너의 모든 클라우드 데이터를 공유로 가져오게 됩니다.

## <a name="restore-edge-local-shares"></a>Edge 로컬 공유 복원

잠재적인 디바이스 실패에 대비하기 위해 다음 백업 솔루션 중 하나를 배포하여 Kubernetes 또는 IoT 워크로드에서 로컬 공유 데이터를 보호했을 수 있습니다.

| 타사 소프트웨어           | 솔루션 참조                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 자세한 내용은 Cohesity에 문의하세요.          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 자세한 내용은 Commvault에 문의하세요. |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 자세한 내용은 Veritas에 문의하세요.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 자세한 내용은 Veeam에 문의하세요. |

교체 디바이스가 완전히 구성된 후에는 로컬 스토리지에 디바이스를 사용하도록 설정합니다. 

다음 단계에 따라 로컬 공유의 데이터를 복구합니다.

1. [디바이스에서 컴퓨팅을 구성](azure-stack-edge-gpu-deploy-configure-compute.md)합니다.
1. 다시 [로컬 공유를 추가](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)합니다.
1. 선택한 데이터 보호 솔루션에서 제공하는 복구 절차를 실행합니다. 이전 표에서 참조를 확인하세요.

## <a name="restore-vm-files-and-folders"></a>VM 파일 및 폴더 복원

잠재적인 디바이스 실패에 대비하기 위해 다음 백업 솔루션 중 하나를 배포하여 VM에서 데이터를 보호했을 수 있습니다.



| 백업 솔루션        | 지원되는 OS   | 참조                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup용 MARS(Microsoft Azure Recovery Services) 에이전트 | Windows        | [MARS 에이전트 정보](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure 통합, 백업 및 복구 솔루션 요약](https://www.cohesity.com/solution/cloud/azure) <br>자세한 내용은 Cohesity에 문의하세요.                          |
| Commvault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 자세한 내용은 Commvault에 문의하세요.
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> 자세한 내용은 Veritas에 문의하세요.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 자세한 내용은 Veeam에 문의하세요. |

교체 디바이스가 완전히 구성된 후 이전에 사용한 VM 이미지를 사용하여 VM을 다시 배포할 수 있습니다. 

다음 단계를 수행하여 VM의 데이터를 복구합니다.
 
1. 디바이스의 [VM 이미지에서 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)합니다. 
1. VM에 선택한 데이터 보호 솔루션을 설치합니다.
1. 선택한 데이터 보호 솔루션에서 제공하는 복구 절차를 실행합니다. 이전 표에서 참조를 확인하세요.

## <a name="restore-a-kubernetes-deployment"></a>Kubernetes 배포 복원

Azure Arc를 통해 Kubernetes 배포를 수행한 경우 허용할 수 없는 디바이스 실패 이후 배포를 복원할 수 있습니다. 애플리케이션 정의가 저장된 `git` 리포지토리에서 고객 애플리케이션/컨테이너를 다시 배포해야 합니다. [Azure Arc를 사용한 Kubernetes 배포에 관한 정보](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro 디바이스 반환](azure-stack-edge-return-device.md) 방법을 알아봅니다.
