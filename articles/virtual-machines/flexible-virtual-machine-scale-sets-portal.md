---
title: Azure Portal을 사용하여 유연한 확장 집합에서 가상 머신 만들기
description: Azure Portal에서 유연한 오케스트레이션 모드로 가상 머신 확장 집합 만드는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/25/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3f6b0512d97b78111be5d88d20bf7800ebe9937c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008506"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Azure Portal을 사용하여 유연한 확장 집합에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

이 문서에서는 Azure Portal을 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 


> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="log-in-to-azure"></a>Azure에 로그인
Azure Portal ( https://portal.azure.com ) 에 로그인합니다.


## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

RHEL, CentOS, Ubuntu 또는 SLES와 같은 Windows Server 이미지 또는 Linux 이미지를 사용하여 확장 집합을 배포할 수 있습니다.

1. Azure Portal 검색 표시줄에서 **가상 머신 확장 집합을** 검색하여 선택합니다.
1. **가상 머신 확장 집합** 페이지에서 **만들기** 를 선택합니다.

1. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되어 있는지 확인하고 리소스 그룹 목록에서 *myVMSSResourceGroup* 을 선택합니다.  
1. **확장 집합 세부 정보에서** 확장 집합 이름으로 *myScaleSet을* 설정하고 해당 영역에 가까운 **지역을** 선택합니다.
1. **오케스트레이션에서 오케스트레이션** **모드** 에 대한 *유연한* 옵션을 선택합니다. 
1. **인스턴스 세부 정보** 아래에서 **이미지에** 대한 마켓플레이스 이미지를 선택합니다. 이 예제에서는 *Ubuntu Server 18.04 LTS* 를 선택했습니다.
1. 원하는 사용자 이름을 입력한 후 원하는 인증 유형을 선택합니다.
   - **암호** 는 12자 이상 길이여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 등 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다. 자세한 내용은 [사용자 이름 및 암호 요구 사항](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)을 참조하세요.
   - Linux OS 디스크 이미지를 선택하는 경우 **SSH 공개 키** 를 대신 선택할 수 있습니다. 공개 키만을 제공합니다(예: *~/.ssh/id_rsa.pub*). 포털에서 Azure Cloud Shell을 사용하여 [SSH 키를 만들고 사용](../virtual-machines/linux/mac-create-ssh-keys.md)할 수 있습니다.

1. **다음을** 선택하여 다음 페이지를 이동합니다. 

1. 디스크 페이지의 기본값을 그대로 **둡니다.**

1. **다음을** 선택하여 다음 페이지를 이동합니다. 

1. **네트워킹** 페이지의 **부하 분산에서 부하 분산기** 사용 확인란을 선택하여 확장 집합 인스턴스를 **부하** 분산기 뒤에 배치합니다. 
1. **부하 분산 옵션** 에서 **Azure Load Balancer** 를 선택합니다.
1. **부하 분산기 선택에서 부하** 분산을 선택하거나 새로 만듭니다.
1. **백 엔드 풀 선택** 에서 **새로 만들기** 를 선택하고 *myBackendPool* 을 입력한 다음, **만들기** 를 선택합니다.

    > [!NOTE]
    > 유연한 확장 집합의 네트워킹에 대한 관련 정보는 유연한 확장 [집합에 대한 확장 가능한 네트워크 연결을 참조하세요.](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity)

1. **다음을** 선택하여 다음 페이지를 이동합니다.

1. 크기 **조정** 페이지에서 초기 **인스턴스 수** 필드를 *5로* 설정합니다. 이 숫자는 최대 1000까지 설정할 수 있습니다. 
1. 크기 **조정 정책의** 경우 *수동으로 유지합니다.* 

1. 완료되면 **검토 + 만들기** 를 선택합니다. 
1. 유효성 검사를 통과하면 **만들기** 를 선택하여 확장 집합을 배포합니다.


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않을 때 리소스 그룹, 확장 집합 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 확장 집합의 리소스 그룹을 선택하고 **삭제** 를 선택합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 유연한 규모를 만드는 방법을 알아봅니다.](flexible-virtual-machine-scale-sets-cli.md)