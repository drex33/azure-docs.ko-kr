---
title: 빠른 시작 - Azure Portal에서 Intel SGX VM 만들기
description: Azure Portal에서 Intel SGX VM을 신속하게 만드는 방법을 학습하여 배포 시작
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: JenCook
ms.custom: mode-portal, ignite-fall-2021
ms.openlocfilehash: ebb48c3c3b0f7273b7ceeebd8a615b19444dab5b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508403"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Intel SGX VM 만들기

이 자습서에서는 Azure Portal을 사용하여 Intel SGX VM을 배포하는 과정을 설명합니다. 그렇지 않으면 [Azure Marketplace](quick-create-marketplace.md) 템플릿을 따르는 것이 좋습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 무료 평가판 계정에는 이 자습서의 VM에 대한 액세스 권한이 없습니다. 종량제 구독으로 업그레이드하세요.


## <a name="sign-in-to-azure"></a>Azure에 로그인

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 위쪽에서 **리소스 만들기** 를 선택합니다.

1. 왼쪽 창에서 **컴퓨팅** 을 선택합니다.

1. **가상 머신 만들기** 를 선택합니다.

    ![VM 배포](media/quick-create-portal/compute-virtual-machine.png)

## <a name="configure-an-intel-sgx-virtual-machine"></a>Intel SGX 가상 머신 구성

1. **기본** 탭에서 **구독** 및 **리소스 그룹** 을 선택합니다.

1. **가상 머신 이름** 에 대해 새 VM의 이름을 입력합니다.

1. 다음 값을 입력하거나 선택합니다.

   * **지역**: 적합한 Azure 지역을 선택합니다.

        > [!NOTE]
        > Intel SGX VM은 특정 지역의 특수한 하드웨어에서 실행됩니다. 최신 지역의 가용성을 보려면 [사용 가능한 지역](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)에서 DCsv2 시리즈 또는 DCsv3/DCdsv3 시리즈를 찾으십시오.

1. 가상 머신에 사용할 운영 체제 이미지를 구성합니다.

    * **이미지 선택**: 이 자습서의 경우 Ubuntu 20.04 LTS - Gen 2를 선택합니다. Ubuntu 18.04 LTS - Gen2 또는 Windows Server 2019를 선택할 수도 있습니다.
    
    * **2세대로 업데이트**: 이미지 아래에서 **VM 생성 구성** 을 선택하고 플라이아웃에서 **2세대** 를 선택합니다.
    
        ![이미지](https://user-images.githubusercontent.com/63871188/137009767-421ee49a-ded8-4cfd-ac53-a3d6750880b9.png)


1. **크기 변경** 을 선택하여 크기 선택기에서 Intel SGX가 있는 가상 머신을 선택합니다. 가상 머신 크기 선택기에서 **모든 필터 지우기** 를 클릭합니다. **필터 추가** 를 선택하고, 필터 형식에 대해 **제품군** 을 선택한 다음, **기밀 컴퓨팅** 만 선택합니다.

    ![DCsv2 시리즈 VM](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > **DC(number)s_v2**, **DC(number)s_v3** 및 **DC(number)ds_v3** 크기가 나타나야 합니다. [자세히 알아보기](virtual-machine-solutions-sgx.md).

1. 다음 정보를 입력합니다.

   * **인증 형식**: Linux VM을 만드는 경우 **SSH 공개 키** 를 선택합니다. 

        > [!NOTE]
        > 인증을 위해 SSH 공개 키를 사용할지 아니면 암호를 사용할지 선택할 수 있습니다. SSH는 더 안전합니다. SSH 키를 생성하는 방법에 대한 지침은 [Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기](../virtual-machines/linux/mac-create-ssh-keys.md)를 참조하세요.

    * **사용자 이름**: VM에 사용할 관리자 이름을 입력합니다.

    * **SSH 공개 키**: 해당하는 경우 RSA 공개 키를 입력합니다.
    
    * **암호**: 해당하는 경우 인증 암호를 입력합니다.

    * **공용 인바운드 포트**: **선택한 포트 허용** 을 선택하고, **공용 인바운드 포트 선택** 목록에서 **SSH(22)** 및 **HTTP(80)** 를 선택합니다. Windows VM을 배포하는 경우 **HTTP(80)** 및 **RDP(3389)** 를 선택합니다.  

    >[!Note]
    > 프로덕션 배포에는 RDP/SSH 포트를 허용하지 않는 것이 좋습니다.  

     ![인바운드 포트](media/quick-create-portal/inbound-port-virtual-machine.png)


1. **디스크** 탭에서 변경을 수행합니다.

   * **DCsv2 시리즈** 는 DC1, DC2 및 DC4에서 **표준 SSD**, **프리미엄 SSD** 가 지원됩니다. 
   * **DCsv3 및 DCdsv3 시리즈** 는 **표준 SSD**, **프리미엄 SSD** 및 **Ultra Disk** 를 지원합니다.

1. 다음 탭에서 설정을 원하는 대로 변경하거나 기본 설정을 유지합니다.

    * **네트워킹**
    * **관리**
    * **게스트 구성**
    * **태그**

1. **검토 + 만들기** 를 선택합니다.

1. **검토 + 만들기** 창에서 **만들기** 를 선택합니다.

> [!NOTE]
> Linux VM을 배포한 경우 다음 섹션으로 이동하여 이 자습서를 계속 진행합니다. Windows VM을 배포한 경우 [다음 단계에 따라 Windows VM에 연결](../virtual-machines/windows/connect-logon.md)한 다음, [OE SDK를 Windows에 설치](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)합니다.


## <a name="connect-to-the-linux-vm"></a>Linux VM에 연결

이미 BASH 셸을 사용하는 경우 **ssh** 명령을 사용하여 Azure VM에 연결합니다. 다음 명령에서 VM 사용자 이름 및 IP 주소를 바꿔서 Linux VM에 연결합니다.

```bash
ssh azureadmin@40.55.55.555
```

VM의 공용 IP 주소는 Azure Portal에 있는 가상 머신의 [개요] 섹션 아래에서 확인할 수 있습니다.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Azure Portal의 IP 주소":::

Windows에서 실행 중이고 BASH 셸이 설치되지 않은 경우 PuTTY와 같은 SSH 클라이언트를 설치합니다.

1. [PuTTY 다운로드 및 설치](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. PuTTY를 실행합니다.

1. PuTTY 구성 화면에서 VM의 공용 IP 주소를 입력합니다.

1. **열기** 를 선택하고 프롬프트에 사용자 이름 및 암호를 입력합니다.

Linux VM에 연결하는 방법에 대한 자세한 내용은 [포털을 사용하여 Azure에서 Linux VM 만들기](../virtual-machines/linux/quick-create-portal.md)를 참조하세요.

> [!NOTE]
> 레지스트리에서 캐시되지 않은 서버의 호스트 키에 대한 PuTTY 보안 경고가 표시되는 경우 다음 옵션 중에서 선택합니다. 이 호스트를 신뢰하는 경우 **예** 를 선택하여 PuTTy의 캐시에 키를 추가하여 연결을 계속합니다. 한 번만 연결을 수행하려는 경우 캐시에 키를 추가하지 않고 **아니요** 를 선택합니다. 이 호스트를 신뢰하지 않는 경우 **취소** 를 선택하여 연결을 중단합니다.

## <a name="install-azure-dcap-client"></a>Azure DCAP 클라이언트 설치

> [!NOTE]
> THIM(신뢰할 수 있는 하드웨어 ID 관리)은 다양한 TEE(신뢰 실행 환경)의 하드웨어 ID를 관리하는 데 도움이 되는 무료 Azure 서비스입니다. Intel PCS(프로비저닝 인증 서비스)에서 참고 자료를 가져와서 캐시합니다. 이 서비스는 증명 목적으로 최소 TCB(신뢰할 수 있는 컴퓨팅 기준) 수준을 Azure 보안 기준으로 적용합니다. DCsv3 및 DCdsv3 시리즈 Azure VM의 경우 Intel 인증서는 VM에서 Intel 서비스를 직접 호출할 수 없기 때문에 THIM에서 가져올 수만 있습니다. 

Intel® Xeon 확장 가능한 프로세서 릴리스에서는 원격 증명 지원이 변경됩니다. DCsv3 및 DCdsv3는 [ECDSA 기반 증명](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html)만 지원하며 사용자는 증명 프로세스 중에 THIM과 상호 작용하고 인용문 생성을 위해 TEE 참고 자료를 가져오도록 [Azure DCAP](https://github.com/Microsoft/Azure-DCAP-Client) 클라이언트를 설치하는 것이 좋습니다. DCsv2는 [EPID 기반 증명](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html)을 계속 지원합니다. 

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 

가상머신에 대한 리소스 그룹을 선택한 다음 **삭제** 를 선택합니다. 리소스 그룹의 이름을 확인하고 리소스 삭제를 마칩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Intel SGX VM에 배포하고 연결했습니다. 자세한 내용은 [Virtual Machines의 솔루션](virtual-machine-solutions-sgx.md)을 참조하세요. 

GitHub의 Open Enclave SDK 샘플로 계속 진행하여 기밀 컴퓨팅 애플리케이션을 빌드하는 방법을 알아봅니다. 

> [!div class="nextstepaction"]
> [Open Enclave SDK 샘플 빌드](https://github.com/openenclave/openenclave/blob/master/samples/README.md)

Microsoft Azure 증명은 여러 TEE의 신뢰성과 내부에서 실행되는 이진의 무결성을 원격으로 확인하기 위한 ECDSA 기반의 무료 증명 프레임워크입니다. [자세히](/azure/attestation/overview) 알아보세요.

