---
title: 프라이빗 클라우드 권한 높이기
titleSuffix: Azure VMware Solution by CloudSimple
description: vCenter의 관리 기능에 대한 프라이빗 클라우드의 권한을 에스컬레이션하는 방법에 대해 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8e61afc395c793204c4da672a2ae9b9ff19c237b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322287"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>CloudSimple 포털에서 프라이빗 클라우드 vCenter 권한 에스컬레이션

프라이빗 클라우드 vCenter에 대한 관리 액세스를 위해 CloudSimple 권한을 일시적으로 에스컬레이션할 수 있습니다.  상승된 권한을 사용하여 VMware 솔루션을 설치하고, ID 원본을 추가하고, 사용자를 관리할 수 있습니다.

VCenter SSO 도메인에 새 사용자를 만들고 vCenter에 대한 액세스 권한을 부여할 수 있습니다.  새 사용자를 만들 때는 vCenter 액세스를 위해 CloudSimple의 기본 제공 그룹에 추가합니다.  자세한 내용은 [VMware vCenter의 Cloudsimple 프라이빗 클라우드 권한 모델](./learn-private-cloud-permissions.md)을 참조하세요.

> [!CAUTION]
> 관리 구성 요소에 대한 구성을 변경하지 마세요. 에스컬레이션된 권한 상태에서 수행되는 작업은 시스템에 부정적인 영향을 주거나 시스템을 사용할 수 없게 만들 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="escalate-privileges"></a>권한 높이기

1. [CloudSimple 포털](access-cloudsimple-portal.md)에 액세스합니다.

2. **리소스** 페이지를 열고 권한을 에스컬레이션할 프라이빗 클라우드를 선택합니다.

3. 요약 페이지의 아래쪽에 있는 **vSphere 권한 변경** 에서 **에스컬레이션** 을 클릭합니다.

    ![vSphere 권한 변경](media/escalate-private-cloud-privilege.png)

4. vSphere 사용자 유형 선택  `CloudOwner@cloudsimple.local` 로컬 사용자만 에스컬레이션할 수 있습니다.

5. 드롭다운에서 에스컬레이션 시간 간격을 선택합니다. 작업을 완료할 수 있는 가장 짧은 기간을 선택합니다.

6. 위험을 이해하고 있는지 확인하려면 이 확인란을 선택합니다.

    ![권한 에스컬레이션 대화 상자](media/escalate-private-cloud-privilege-dialog.png)

7. **확인** 을 클릭합니다.

8. 에스컬레이션 프로세스는 몇 분 정도 걸릴 수 있습니다. 완료한 경우 **확인** 을 클릭합니다.

권한 에스컬레이션이 시작되고 선택된 간격이 끝날 때까지 지속됩니다.  프라이빗 클라우드 vCenter에 로그인하여 관리 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 사용자 한 명만 상승된 권한을 가질 수 있습니다.  한 사용자의 권한 에스컬레이션을 해제해야 다른 사용자의 권한을 에스컬레이션할 수 있습니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자-그룹*, *클라우드-글로벌 클러스터-관리자-그룹*, *클라우드-글로벌-스토리지 관리자-그룹*, *클라우드-글로벌-네트워크-관리자-그룹* 또는 *클라우드-글로벌-VM 관리자-그룹* 에만 추가해야 합니다.  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *관리자* 그룹에 추가해야 하며, 서비스 계정은 vSphere 웹 UI에 로그인하는 데 사용하면 안 됩니다.

## <a name="extend-privilege-escalation"></a>권한 상승 확장

작업을 완료하는 데 시간이 더 필요한 경우 권한 에스컬레이션 기간을 확장할 수 있습니다.  관리 작업을 완료할 수 있는 추가 에스컬레이션 시간 간격을 선택합니다.

1. Cloudsimple 포털의 **리소스** > **프라이빗 클라우드** 에서 권한 에스컬레이션을 확장하려는 프라이빗 클라우드를 선택합니다.

2. 요약 탭의 아래쪽에 있는 **권한 에스컬레이션 확장** 을 클릭합니다.

    ![권한 상승 확장](media/de-escalate-private-cloud-privilege.png)

3. 드롭다운에서 에스컬레이션 시간 간격을 선택합니다. 새 에스컬레이션 종료 시간을 검토합니다.

4. **저장** 을 클릭하여 간격을 확장합니다.

## <a name="de-escalate-privileges"></a>권한 에스컬레이션 해제

관리 작업이 완료되면 권한 에스컬레이션을 해제해야 합니다.  

1. Cloudsimple 포털의 **리소스** > **프라이빗 클라우드** 에서 권한 에스컬레이션을 해제할 프라이빗 클라우드를 선택합니다.

2. **에스컬레이션 해제** 를 클릭합니다.

3. **확인** 을 클릭합니다.

> [!IMPORTANT]
> 오류를 방지하려면 vCenter에서 로그아웃하고 권한 에스컬레이션을 해제한 뒤 다시 로그인합니다.

## <a name="next-steps"></a>다음 단계

* [Active Directory를 사용할 vCenter ID 원본 설정](./set-vcenter-identity.md)
* [백업 워크로드 가상 머신](./backup-workloads-veeam.md)에 백업 솔루션 설치
