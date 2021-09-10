---
title: 데이터 센터에서 Azure Portal을 통해 Azure Stack Edge Pro FPGA 배포를 준비하기 위한 자습서
description: Azure Stack Edge Pro FPGA 배포에 대한 첫 번째 자습서에는 Azure Portal을 준비하는 작업이 포함됩니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: alkohli
ms.custom: subject-rbac-steps
ms.openlocfilehash: d215c1cc8650eb23d1702f7ef0fd782278cfb6d8
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605208"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-fpga"></a>자습서: Azure Stack Edge Pro FPGA 배포 준비  

이 문서는 Azure Stack Edge Pro FPGA를 완전히 배포하는 데 필요한 배포 자습서 시리즈 중 첫 번째 자습서입니다. 이 자습서에서는 Azure Stack Edge 리소스를 배포하기 위해 Azure Portal을 준비하는 방법에 대해 설명합니다. 

설치 및 구성 프로세스를 완료하려면 관리자 권한이 필요합니다. 포털 준비에는 10분 미만이 소요됩니다.  

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 새 리소스 만들기
> * 활성화 키 가져오기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="get-started"></a>시작하기

Azure Stack Edge Pro FPGA를 배포하려면 다음 자습서를 지정된 순서대로 참조하세요.

| **#** | **단계** | **이러한 문서를 사용합니다.** |
| --- | --- | --- | 
| 1. |**[Azure Stack Edge Pro FPGA 배포를 위한 Azure Portal 준비](azure-stack-edge-deploy-prep.md)** |Azure Stack Box Edge 물리적 디바이스를 설치하기 전에 Azure Stack Edge 리소스를 만들고 구성합니다. |
| 2. |**[Azure Stack Edge Pro FPGA 설치](azure-stack-edge-deploy-install.md)**|Azure Stack Edge Pro FPGA 물리적 디바이스의 포장을 풀고, 랙에 탑재하고, 케이블을 연결합니다.  |
| 3. |**[Azure Stack Edge Pro FPGA 연결, 설정 및 활성화](azure-stack-edge-deploy-connect-setup-activate.md)** |로컬 웹 UI에 연결하고 디바이스 설정을 완료한 다음, 디바이스를 활성화합니다. 디바이스에서 SMB 또는 NFS 공유를 설정할 준비가 되었습니다.  |
| 4. |**[Azure Stack Edge Pro FPGA를 사용하여 데이터 전송](azure-stack-edge-deploy-add-shares.md)** |공유를 추가하고 SMB 또는 NFS를 통해 공유에 연결합니다. |
| 5. |**[Azure Stack Edge Pro FPGA를 사용하여 데이터 변환](azure-stack-edge-deploy-configure-compute.md)** |디바이스에서 컴퓨팅 모듈을 구성하여 Azure로 이동할 때 데이터를 변환합니다. |

이제 Azure Portal 설치를 시작할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음은 Azure Stack Edge 리소스, Azure Stack Edge Pro FPGA 디바이스 및 데이터 센터 네트워크의 구성 필수 조건입니다.

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge 리소스

시작하기 전에 다음 사항을 확인합니다.

* Data Stack Edge 리소스에 대해 Microsoft Azure 구독이 활성화되어 있습니다. [Microsoft EA(기업계약)](https://azure.microsoft.com/overview/sales-number/), [CSP(클라우드 솔루션 공급자)](/partner-center/azure-plan-lp) 또는 [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/)과 같은 지원되는 구독을 사용했는지 확인합니다. 종량제 구독은 지원되지 않습니다.

* RBAC 역할: Azure RBAC(역할 기반 액세스 제어)에는 다음과 같은 역할 할당이 있습니다.

  * Azure Stack Edge, IoT Hub, Azure Storage 리소스를 만들려면 리소스 그룹 범위에서 사용자에게 기여자 또는 소유자 역할이 있어야 합니다.

  * 리소스 그룹 범위에서 사용자에게 기여자 역할을 할당하려면 구독 범위에서 소유자 역할이 있어야 합니다.

  세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

* 리소스 공급자: 다음 리소스 공급자가 등록되어 있습니다. 

  * Azure Stack Edge/Data Box Gateway 리소스를 만들려면 `Microsoft.DataBoxEdge` 공급자가 등록되어 있는지 확인합니다.

  * IoT Hub 리소스를 만들려면 `Microsoft.Devices` 공급자가 등록되어 있는지 확인합니다.

  * Azure Storage 리소스를 만들려면 Azure Storage가 등록되어 있는지 확인합니다. Azure SRP(Storage 리소스 공급자)는 기본적으로 등록된 리소스 공급자이지만 경우에 따라 등록해야 할 수도 있습니다.

  **리소스 공급자를 등록하려면 위의 관련 RBAC 역할이 할당되어 있어야 합니다.**

  등록 방법에 대한 자세한 내용은 [리소스 공급자 등록](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)을 참조하세요.

* Azure Active Directory Graph API에 대한 관리자 또는 사용자 액세스 권한이 있습니다. 자세한 내용은 [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-)를 참조하세요.

* 액세스 자격 증명이 있는 Microsoft Azure Storage 계정이 있습니다.

* 시스템 관리자가 설정한 Azure Policy 할당에 의해 차단되지 않습니다. Azure Policy에 대한 자세한 내용은 [빠른 시작: 비준수 리소스를 식별하는 정책 할당 만들기](../governance/policy/assign-policy-portal.md)를 참조하세요.


### <a name="for-the-azure-stack-edge-pro-fpga-device"></a>Azure Stack Edge Pro FPGA 디바이스

물리적 디바이스를 배포하기 전에 먼저 다음 사항을 확인합니다.

* 배송 패키지에 포함된 안전 정보를 검토했습니다.
* 데이터 센터의 표준 19" 랙에 디바이스를 랙에 탑재할 수 있는 하나의 1U 슬롯이 있습니다.
* 디바이스가 안전하게 있을 수 있는 평평하고 안정된 수평 작업대를 사용할 수 있습니다.
* 디바이스를 설치하려는 사이트에 독립적인 전원의 표준 AC 전원 디바이스가 있거나 UPS(무정전 전원 공급 디바이스)가 장착된 PDU(랙 전원 분배 디바이스)가 있습니다.
* 물리적 디바이스에 대한 액세스 권한이 있습니다.

### <a name="for-the-datacenter-network"></a>데이터 센터 네트워크의 경우

시작하기 전에 다음 사항을 확인합니다.

* 데이터 센터의 네트워크가 Azure Stack Edge Pro FPGA 디바이스의 네트워킹 요구 사항에 따라 구성되어 있습니다. 자세한 내용은 [Azure Stack Edge Pro FPGA 시스템 요구 사항](azure-stack-edge-system-requirements.md)을 참조하세요.

* Azure Stack Edge Pro FPGA가 정상 작동하기 위한 조건은 다음과 같습니다.

  * 디바이스가 업데이트 상태를 유지하기 위한 10Mbps의 최소 다운로드 대역폭.
  * 파일을 전송하기 위한 20Mbps의 최소 전용 업로드 및 다운로드 대역폭.

## <a name="create-new-resource-for-existing-device"></a>기존 디바이스에 대한 새 리소스 만들기

기존 Azure Stack Edge Pro FPGA 고객인 경우 기존 디바이스를 교체하거나 다시 설정해야 하는 경우 다음 절차를 사용하여 새 리소스를 만듭니다.

신규 고객인 경우 워크로드에 대한 Azure Stack Edge Pro - GPU 디바이스를 사용하여 탐색하는 것이 좋습니다. 자세한 내용은 [GPU를 사용하는 Edge Pro Azure Stack Pro란?](azure-stack-edge-gpu-overview.md)을 참조하세요. GPU 디바이스가 있는 Azure Stack Edge Pro를 주문하는 방법에 대한 자세한 내용은 [Azure Stack Edge Pro - GPU에 대한 새 리소스 만들기](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)를 참조하세요.

기존 디바이스에 대한 새 Azure Stack Edge 리소스를 만들려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음에 로그인합니다.

    - URL [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인하거나
    - 또는 URL [https://portal.azure.us](https://portal.azure.us)에서 Azure Government 포털에 로그인합니다. 자세한 내용을 보려면 [포털을 사용하여 Azure Government에 연결](../azure-government/documentation-government-get-started-connect-with-portal.md)로 이동합니다.

1. **+ 리소스 만들기** 를 선택합니다. **Azure Stack Edge** 를 검색하여 선택합니다. 그런 다음 **만들기** 를 선택합니다.

1. Azure Stack Edge Pro FPGA 디바이스에 대한 구독 및 **배송 대상** 에서 디바이스를 배송할 국가를 선택합니다.

   ![디바이스에 대한 구독 및 배송 국가 선택](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. 표시되는 디바이스 유형 목록에서 **Azure Stack Edge Pro - FPGA** 를 선택합니다. 그런 다음, **선택** 을 선택합니다. 

   **Azure Stack Edge Pro - FPGA** 디바이스 유형은 기존 디바이스가 있는 경우에만 표시됩니다. 새 디바이스를 주문해야 하는 경우 [Azure Stack Edge Pro - GPU에 대한 새 리소스 만들기](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)로 이동합니다.

   ![Azure Stack Edge 서비스 검색](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. **기본 사항** 탭에서 다음을 수행합니다.

   1. 다음 **프로젝트 세부 정보** 를 입력하거나 선택합니다.
    
       |설정  |값  |
       |---------|---------|
       |구독    |이 값은 이전에 선택한 내용에 따라 자동으로 채워집니다. 구독은 청구 계정에 연결됩니다. |
       |Resource group  |기존 그룹을 선택하거나 새 그룹을 만듭니다.<br>[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)에 대해 자세히 알아봅니다.     |

   1. 다음 **인스턴스 세부 정보** 를 입력하거나 선택합니다.

       |설정  |값  |
       |---------|---------|
       |속성   | 리소스를 식별하기 위한 이름.<br>이름은 문자, 숫자 및 하이픈을 포함하여 2~50자입니다.<br> 이름은 문자 또는 숫자로 시작하고 끝납니다.        |
       |지역     |Azure Stack Edge 리소스를 사용할 수 있는 모든 지역 목록을 보려면 [지역별로 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)을 참조하세요. Azure Government를 사용하는 경우 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에서 본 것처럼 모든 정부 지역을 사용할 수 있습니다.<br> 디바이스를 배포하려는 지역에 지리적으로 가장 가까운 위치를 선택합니다.|

   1. **검토 + 만들기** 를 선택합니다.

    ![프로젝트 및 인스턴스 세부 정보](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. **검토 + 만들기** 탭에서 **사용 약관**, **가격 세부 정부** 및 리소스에 대한 세부 정보를 검토합니다. 그런 다음 **만들기** 를 선택합니다.

    ![Azure Stack Edge 리소스 세부 정보 및 개인정보처리방침 검토](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. 리소스 생성에는 몇 분 정도가 소요됩니다. 리소스가 생성되고 배포된 후에는 알림이 표시됩니다. **리소스로 이동** 을 선택합니다.

   ![Azure Stack Edge 리소스로 이동](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

주문이 완료되면 Microsoft에서 주문을 검토하고 배송 세부 정보가 포함된 이메일을 통해 연락을 드립니다.

![Azure Stack Edge Pro FPGA 주문 검토 알림](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>활성화 키 가져오기

Azure Stack Edge 리소스가 가동되면 활성화 키를 가져와야 합니다. 이 키는 Azure Stack Edge Pro FPGA 디바이스를 활성화하고 리소스와 연결하는 데 사용됩니다. 이제 Azure Portal에서 이 키를 얻을 수 있습니다.

1. 사용자가 만든 리소스로 이동하여 **개요** 를 선택합니다. 주문이 처리 중이라는 알림이 표시됩니다.

    ![개요 선택](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. 주문이 처리되고 디바이스가 사용 중이면 **개요** 가 업데이트됩니다. 기본 **Azure Key Vault 이름** 을 적용하거나 새 이름을 입력합니다. **활성화 키 생성** 을 선택합니다. 복사 아이콘을 선택하여 키를 복사하고 나중에 사용할 수 있도록 저장합니다.

    ![활성화 키 가져오기](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * 활성화 키는 생성되고 3일 후에 만료됩니다.
> * 키가 만료된 경우 새 키를 만듭니다. 이전 키는 유효하지 않습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Azure Stack Edge Pro FPGA 토픽에 대해 알아보았습니다.

> [!div class="checklist"]
>
> * 새 리소스 만들기
> * 활성화 키 가져오기

Azure Stack Edge Pro FPGA를 설치하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro FPGA 설치](./azure-stack-edge-deploy-install.md)