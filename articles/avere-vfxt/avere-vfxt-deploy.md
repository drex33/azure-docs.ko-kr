---
title: Avere vFXT for Azure 배포
description: Azure Marketplace에서 제공하는 배포 마법사를 사용하여 Avere vFXT for Azure에서 클러스터를 배포하는 방법을 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 330fa1368c078f1e44dd436cd3bea6407c6f4cc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794655"
---
# <a name="deploy-the-vfxt-cluster"></a>vFXT 클러스터 배포

이 절차에서는 Azure Marketplace에서 제공되는 배포 마법사를 사용하는 과정을 안내합니다. 이 마법사는 Azure Resource Manager 템플릿을 사용하여 클러스터를 자동으로 배포합니다. 양식에 매개 변수를 입력하고 **만들기** 를 클릭하면 Azure에서 다음 작업이 자동으로 완료됩니다.

* 클러스터를 배포하고 관리하는 데 필요한 소프트웨어가 포함된 기본 VM인 클러스터 컨트롤러를 만듭니다.
* 리소스 그룹 및 가상 네트워크 인프라를 설정하고 새 요소를 만듭니다.
* 클러스터 노드 VM을 만들어 Avere 클러스터로 구성합니다.
* 요청이 표시되면 새 Azure Blob 컨테이너를 만들어 클러스터 코어 파일러로 구성합니다.

이 문서의 지침을 따르면 다음 다이어그램과 같이 가상 네트워크, 서브넷, 클러스터 컨트롤러 및 vFXT 클러스터가 갖추어집니다. 이 다이어그램은 새 Blob Storage 컨테이너(새 스토리지 계정에 있음, 표시되지 않음)와 서브넷 내 Microsoft 스토리지에 대한 서비스 엔드포인트를 포함하는 선택적 Azure Blob 코어 파일러를 보여 줍니다.

![Avere 클러스터 구성 요소와 함께 동심 사각형 3개를 보여 주는 다이어그램 바깥쪽 사각형에는 ‘리소스 그룹’이라고 레이블이 지정되어 있고, 그 안에는 ‘Blob Storage(선택 사항)’라고 레이블이 지정된 육각형이 있습니다. 다음 안쪽 사각형에는 ‘가상 네트워크: 10.0.0.0/16’이라는 레이블이 지정되어 있고 고유 구성 요소가 포함되어 있지 않습니다. 가장 안쪽 사각형에는 ‘서브넷: 10.0.0.0/24’라고 레이블이 지정되어 있고 ‘클러스터 컨트롤러’라는 레이블이 지정된 VM, ‘vFXT 노드(vFXT 클러스터)’라는 레이블이 지정된 VM 3개와 ‘서비스 엔드포인트’라고 레이블이 지정된 육각형이 있습니다. (서브넷 내부에 있는) 서비스 엔드포인트와 (서브넷과 VNet 외부에 있고 리소스 그룹 내에 있는) Blob Storage를 연결하는 화살표가 있습니다. 이 화살표는 서브넷 및 가상 네트워크 경계를 통과합니다.](media/avere-vfxt-deployment.png)

만들기 템플릿을 사용하기 전에 다음 필수 구성 요소가 충족되었는지 확인하세요.  

* [새 구독](avere-vfxt-prereqs.md#create-a-new-subscription)
* [구독 소유자 권한](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 클러스터에 대한 할당량](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [스토리지 서비스 엔드포인트(필요한 경우)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -기존 가상 네트워크를 사용하고 Blob Storage를 만드는 배포에 필요합니다.

클러스터 배포 단계 및 계획에 대한 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md) 및 [배포 개요](avere-vfxt-deploy-overview.md)를 참조하세요.

## <a name="create-the-avere-vfxt-for-azure"></a>Avere vFXT for Azure 만들기

Avere를 검색한 다음 “Avere vFXT for Azure ARM Template”(Avere vFXT for Azure ARM 템플릿)를 선택하여 Azure Portal에서 만들기 템플릿에 액세스합니다.

![브라우저 창에 이동 경로 "새로 만들기 > Marketplace > 모두"와 함께 Azure Portal이 표시됩니다. 모두 페이지의 검색 필드에 검색어 “avere”가 표시되고 두 번째 결과 “Avere vFXT for Azure ARM Template”(Avere vFXT for Azure ARM 템플릿)의 외곽선이 빨간색으로 강조 표시됩니다.](media/avere-vfxt-template-choose.png)

Avere vFXT for Azure ARM 템플릿 페이지에서 세부 정보를 읽은 다음 **만들기** 단추를 클릭하여 시작합니다.

![배포 템플릿의 첫 페이지가 표시된 Azure Marketplace](media/avere-vfxt-deploy-first.png)

템플릿은 4단계로 구성되어 있습니다. 그중 두 단계는 정보 수집 페이지이고 나머지 두 단계는 각각 유효성 검사 및 확인 단계입니다.

* 1페이지에서는 클러스터 컨트롤러 VM에 대한 설정을 수집합니다.
* 2페이지에서는 클러스터와 추가 리소스(예: 서브넷, 스토리지)를 만들기 위한 매개 변수를 수집합니다.
* 3페이지에서는 선택 항목을 요약하고 구성의 유효성을 검사합니다.
* 소프트웨어 사용 약관 설명이 표시되는 4페이지에서 클러스터 만들기 프로세스를 시작할 수 있습니다.

## <a name="page-one-parameters---cluster-controller-information"></a>1페이지 매개 변수 - 클러스터 컨트롤러 정보

배포 템플릿의 1페이지는 주로 클러스터 컨트롤러 관련 내용입니다.

![배포 템플릿 1페이지](media/avere-vfxt-deploy-1.png)

다음 정보를 입력합니다.

* **클러스터 컨트롤러 이름** - 클러스터 컨트롤러 VM의 이름을 설정합니다.

* **컨트롤러 사용자 이름** - 클러스터 컨트롤러 VM의 루트 사용자 이름을 설정합니다.

* **인증 유형** - 컨트롤러에 연결하는 데 사용할 인증 유형(암호 또는 SSH 공개 키 인증)을 선택합니다. SSH 공개 키 방법을 사용하는 것이 좋습니다. 도움이 필요한 경우 [SSH 키를 만들고 사용하는 방법](../virtual-machines/linux/ssh-from-windows.md)을 참조하세요.

* **암호** 또는 **SSH 공개 키** - 선택한 인증 유형에 따라 다음 필드에 RSA 공개 키 또는 암호를 입력해야 합니다. 앞에서 입력한 사용자 이름과 함께 이 자격 증명을 사용합니다.

* **구독** - Avere vFXT의 구독을 선택합니다.

* **리소스 그룹** - 기존의 빈 Avere vFXT 클러스터용 리소스 그룹을 선택하거나 "새로 만들기"를 클릭하고 새 리소스 그룹 이름을 입력합니다.

* **위치** - 클러스터와 리소스의 Azure 위치를 선택합니다.

작업이 완료되면 **확인** 을 클릭합니다.

> [!NOTE]
> 클러스터 컨트롤러에 공용 IP 주소를 사용하려는 경우 기존 네트워크를 선택하는 대신 클러스터용으로 새 가상 네트워크를 만듭니다. 이 설정은 2페이지에 있습니다.

## <a name="page-two-parameters---vfxt-cluster-information"></a>2페이지 매개 변수 - vFXT 클러스터 정보

배포 템플릿의 2페이지에서는 클러스터 크기, 노드 유형, 캐시 크기 및 스토리지 매개 변수와 같은 여러 설정을 지정할 수 있습니다.

![배포 템플릿 2페이지](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 클러스터 노드 수** - 클러스터의 노드 수를 선택합니다. 최소값은 3개 노드이고 최대 노드는 20개입니다.

* **클러스터 관리 암호** - 클러스터 관리용 암호를 만듭니다. 이 암호와 사용자 이름 ```admin```을 함께 사용하여 클러스터 제어판에 로그인해 클러스터를 모니터링하고 클러스터 설정을 구성합니다.

* **Avere vFXT 클러스터 이름** - 클러스터에 고유한 이름을 지정합니다.

* **크기** - 이 섹션에서는 클러스터 노드에 사용되는 VM 유형을 보여 줍니다. 권장 옵션은 하나뿐이지만 **크기 변경** 링크를 사용하면 이 인스턴스 유형에 대한 세부 정보와 가격 계산기에 대한 링크가 정리된 테이블이 열립니다.

* **노드당 캐시 크기** - 클러스터 캐시는 여러 클러스터 노드에 분산되므로 Avere vFXT 클러스터의 총 캐시 크기는 이 크기에 노드 수를 곱한 값입니다.

  권장 구성: Standard_E32s_v3 노드에 대해 노드당 4TB를 사용합니다.

* **가상 네트워크** - 클러스터를 포함할 새 가상 네트워크를 정의하거나 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)에서 설명하는 전제 조건을 충족하는 기존 네트워크를 선택합니다.

  > [!NOTE]
  > 새 가상 네트워크를 만드는 경우 클러스터 컨트롤러에 공용 IP 주소가 지정되므로 새로운 개인 네트워크에 액세스할 수 있습니다. 기존 가상 네트워크를 선택하는 경우에는 공용 IP 주소 없이 클러스터 컨트롤러가 구성됩니다.
  >
  > 클러스터 컨트롤러에 공개된 IP 주소가 있으면 vFXT 클러스터에 더 쉽게 액세스할 수 있지만 보안상으로는 다소 위험합니다.
  >* 클러스터 컨트롤러의 공용 IP 주소를 사용하면 이 주소를 점프 호스트로 사용하여 프라이빗 서브넷 외부에서 Avere vFXT 클러스터에 연결할 수 있습니다.
  >* 컨트롤러에 공용 IP 주소가 없는 경우 클러스터에 액세스하려면 다른 점프 호스트, VPN 연결 또는 ExpressRoute가 필요합니다. 예를 들어 VPN 연결이 이미 구성된 기존 가상 네트워크를 사용합니다.
  >* 공용 IP 주소가 있는 컨트롤러를 만드는 경우 컨트롤러 VM을 네트워크 보안 그룹으로 보호해야 합니다. 기본적으로 Avere vFXT for Azure 배포에서는 공용 IP 주소가 있는 컨트롤러가 포트 22에만 인바운드 액세스할 수 있도록 제한하는 네트워크 보안 그룹이 생성됩니다. 액세스 범위를 IP 원본 주소 범위로 고정(클러스터 액세스에 사용하려는 컴퓨터의 연결만 허용)하면 시스템을 추가로 보호할 수 있습니다.

  또한 새 가상 네트워크는 Azure Blob Storage에 대한 스토리지 서비스 엔드포인트를 사용하여 구성되고, 네트워크 액세스 제어는 클러스터 서브넷의 IP만 허용하도록 구성됩니다.

* **서브넷** -서브넷을 선택하거나 새 서브넷을 만듭니다.

* **Blob Storage 만들기 및 사용** - **True** 를 선택하여 새 Azure Blob 컨테이너를 만들어 새 Avere vFXT 클러스터용 백 엔드 스토리지로 구성합니다. 이 옵션은 또한 클러스터의 리소스 그룹에 새 스토리지 계정을 만들고 클러스터 서브넷 내에 Microsoft 스토리지 서비스 엔드포인트를 만듭니다.
  
  기존 가상 네트워크를 제공하는 경우 클러스터를 만들기 전에 스토리지 서비스 엔드포인트가 있어야 합니다. 자세한 내용은 [Avere vFXT 시스템 계획](avere-vfxt-deploy-plan.md)을 참조하세요.

  새 컨테이너를 만들지 않으려면 이 필드를 **false** 로 설정합니다. 이 경우 클러스터를 만든 후 스토리지를 연결하고 구성해야 합니다. 지침은 [스토리지 구성](avere-vfxt-add-storage.md)을 읽으세요.

* **(새로 만들기) 스토리지 계정** - 새 Azure Blob 컨테이너를 만드는 경우 새 스토리지 계정 이름을 입력합니다.

## <a name="validation-and-purchase"></a>유효성 검사 및 구매

구성을 요약하고 매개 변수의 유효성을 검사하는 3페이지. 유효성 검사에 성공한 후 요약을 확인하고 **확인** 단추를 클릭합니다.

> [!TIP]
> **확인** 단추 옆의 **템플릿 및 매개 변수 다운로드** 링크를 클릭하여 이 클러스터의 만들기 설정을 저장할 수 있습니다. 이 정보는 예를 들어 재해 복구 시나리오에서 대체 클러스터를 만들 때와 같이 유사한 클러스터를 만들어야 하는 경우에 유용합니다. 자세한 내용은 [재해 복구 지침](disaster-recovery.md)을 참조하세요.

![배포 템플릿 3페이지 - 유효성 검사](media/avere-vfxt-deploy-3.png)

사용 약관과 개인정보취급방침 및 가격 책정 정보 링크가 정리된 4페이지

누락된 연락처 정보를 입력한 다음 **만들기** 단추를 클릭하여 약관에 동의하고 Avere vFXT for Azure 클러스터를 만듭니다.

![배포 템플릿 4페이지 - 약관, 만들기 단추](media/avere-vfxt-deploy-4.png)

클러스터 배포는 15~20분 정도 걸립니다.

## <a name="gather-template-output"></a>템플릿 출력 수집

Avere vFXT 템플릿에서 클러스터 만들기가 완료되면 새 클러스터에 대한 몇 가지 중요 정보가 출력됩니다.

> [!TIP]
> 템플릿 출력에서 **관리 IP 주소** 를 복사해 두세요. 클러스터를 관리하려면 이 주소가 필요합니다.

필요한 정보를 찾으려면 다음을 수행합니다.

1. 클러스터 컨트롤러에 대한 리소스 그룹으로 이동합니다.

1. 왼쪽에서 **배포** 를 클릭한 다음 **microsoft-avere.vfxt-template** 을 클릭합니다.

   ![왼쪽에 배포가 선택되어 있고 배포 이름 아래의 표에 microsoft-avere.vfxt-template이 표시되어 있는 리소스 그룹 포털 페이지](media/avere-vfxt-outputs-deployments.png)

1. 왼쪽에서 **출력** 을 클릭합니다. 각 필드의 값을 복사합니다.

   ![레이블 오른쪽의 필드에 SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs, MGMT_IP 값이 표시된 출력 페이지](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>다음 단계

이제 클러스터가 실행되고 클러스터의 관리 IP 주소를 알고 있으므로 [클러스터 구성 도구에 연결](avere-vfxt-cluster-gui.md)합니다.

구성 인터페이스를 사용하여 다음 설치 작업을 포함해 클러스터를 사용자 지정합니다.

* [지원 사용](avere-vfxt-enable-support.md)
* [스토리지 추가](avere-vfxt-add-storage.md)(필요한 경우)