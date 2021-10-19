---
title: Azure Lab Services로 엔지니어링용 SOLIDWORKS 랩 설정 | Microsoft Docs
description: SOLIDWORKS를 사용하여 엔지니어링 과정을 위한 랩을 설정하는 방법을 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 0b12526362cf7747768b3c4c8829a041dc499728
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176532"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>SOLIDWORKS를 사용하여 엔지니어링 클래스용 랩 설정

[SOLIDWORKS는](https://www.solidworks.com/) 고체를 모델링하기 위한 3D CAD(컴퓨터 지원 디자인) 환경을 제공하며 다양한 종류의 엔지니어링 필드에서 사용됩니다.  SOLIDWORKS를 통해 엔지니어는 디자인을 쉽게 만들고, 시각화하고, 시뮬레이션하고, 문서화할 수 있습니다.

대학이 일반적으로 사용하는 라이선스 옵션은 SOLIDWORKS 네트워크 라이선스입니다.   이 옵션을 사용하면 사용자는 라이선스 서버에서 관리하는 라이선스 풀을 공유합니다.  동시 사용자 수에 충분한 라이선스만 있으면 되므로 이러한 유형의 라이선스를 "부동" 라이선스라고도 합니다.  사용자가 SOLIDWORKS 사용을 완료하면 다른 사용자가 다시 사용할 수 있도록 해당 라이선스가 중앙에서 관리되는 라이선스 풀로 돌아갑니다.

이 문서에서는 SOLIDWORKS 2019 및 네트워크 라이선스를 사용하는 클래스를 설정하는 방법을 설명합니다.

## <a name="license-server"></a>라이선스 서버

SOLIDWORKS 네트워크 라이선스를 사용하려면 라이선스 서버에 SolidNetWork 라이선스 관리자를 설치하고 활성화해야 합니다.  이 라이선스 서버는 일반적으로 온-프레미스 네트워크 또는 Azure 내의 개인 네트워크에 있습니다.  서버에서 SolidNetWork 라이선스 관리자를 설정하는 방법에 대한 자세한 내용은 SOLIDWORKS 설치 가이드의 [라이선스 관리자 설치 및 활성화를](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) 참조하세요.  이를 설정하는 경우, 사용되는 **포트 번호** 와 [**일련 번호**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) 가 이후 단계에서 필요하므로 기억하세요.

라이선스 서버가 설정되면 [VNet(가상 네트워크)](./how-to-connect-peer-virtual-network.md)을 [랩 계정](./tutorial-setup-lab-account.md)에 피어해야 합니다.  네트워크 피어링은 랩을 만들기 전에 완료되어야 랩 가상 머신이 라이선스 서버에 액세스하고 그 반대도 가능해집니다.

> [!NOTE]
> 랩 가상 머신과 라이선스 서버 간의 통신을 허용하려면 방화벽에서 적절한 포트가 열려 있는지 확인해야 합니다.  예를 들어 라이선스 서버의 방화벽에 인바운드 및 아웃바운드 규칙을 추가하는 방법을 보여주는 [Windows 방화벽용 라이선스 관리자 컴퓨터 포트 수정의](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) 지침을 참조하세요.  랩 가상 머신의 포트를 열어야 할 수도 있습니다.  랩의 공용 IP 주소를 가져오는 방법을 비롯한 자세한 내용은 [랩에 대한 방화벽 설정](./how-to-configure-firewall-settings.md) 문서의 단계를 따르세요.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md)의 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지를 지정하는 방법](./specify-marketplace-images.md)에 대한 문서를 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용하도록 설정합니다.|

> [!NOTE]
> Windows 10 외에도 SOLIDWORKS는 다른 버전의 Windows를 지원합니다.  자세한 내용은 [SOLIDWORKS 시스템 요구 사항](https://www.solidworks.com/sw/support/SystemRequirements.html)을 참조하세요.

### <a name="lab-settings"></a>랩 설정

클래스룸 랩을 설정하는 경우 아래 표의 설정을 사용합니다. 클래스룸 랩을 만드는 방법에 대한 자세한 내용은 클래스룸 랩 설정 자습서를 참조하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| **소형 GPU(시각화)**  이 VM은 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다.|  
|가상 머신 이미지| Windows 10 Pro|

> [!NOTE]
> **소형 GPU(시각화)** 가상 머신 크기는 고성능 그래픽 환경을 사용하도록 구성됩니다.  이 가상 머신 크기에 대한 자세한 내용은 [GPU를 사용하여 랩을 설정하는 방법](./how-to-setup-lab-gpu.md)의 문서를 참조하세요.

> [!WARNING]
> 랩 계정용 [가상 네트워크를 라이선스 서버용 가상 네트워크에 피어링하는 작업을](./how-to-connect-peer-virtual-network.md) 랩을 만들기 **이전에** 완료해야 한다는 점을 명심하세요.

## <a name="template-virtual-machine-configuration"></a>템플릿 가상 머신 구성

이 섹션의 단계에서는 SOLIDWORKS 설치 파일을 다운로드하고 클라이언트 소프트웨어를 설치하여 템플릿 가상 머신을 설정하는 방법을 보여줍니다.

1. 템플릿 가상 머신을 시작하고 RDP를 사용하여 컴퓨터에 연결합니다.

1. SOLIDWORKS 클라이언트 소프트웨어용 설치 파일을 다운로드합니다. 배포 옵션에는 다음 두 가지가 있습니다.
   - [SOLIDWORKS 고객 포털](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)에서 다운로드합니다.
   - 서버에 있는 디렉터리에서 다운로드합니다.  이 옵션을 사용하는 경우 템플릿 가상 머신에서 서버에 액세스할 수 있는지 확인해야 합니다.  예를 들어 이 서버는 랩 계정과 피어링되는 동일한 가상 네트워크에 있을 수 있습니다.
  
    자세한 내용은 SOLIDWORKS 설치 가이드에 있는 [SOLIDWORKS에서 개별 컴퓨터에 설치](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0)를 참조하세요.

1. 설치 파일을 다운로드한 후 SOLIDWORKS 설치 관리자를 사용하여 클라이언트 소프트웨어를 설치합니다. SOLIDWORKS 설치 가이드에 있는 [라이선스 클라이언트 설치](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) 에서 세부 정보를 참조하세요.

    > [!NOTE]
    > **서버 추가** 대화 상자에서 라이선스 서버에 사용되는 **포트 번호와** 라이선스 서버의 이름 또는 IP 주소를 묻는 메시지가 표시됩니다.

## <a name="cost"></a>Cost

이 클래스의 가능한 예상 비용을 살펴보겠습니다. 이러한 예상치에는 라이선스 서버를 실행하는 비용이 포함되지 않습니다. 25명의 학생이 있는 클래스를 예로 들겠습니다. 예약된 수업 시간은 20시간입니다. 예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다. 선택한 가상 머신 크기는 **소형 GPU(시각화)** 로, 랩 단위는 160개입니다.

학생 25명 \*(예약 시간 20시간 + 할당 시간 10시간) \* 랩 단위 160개 * 시간당 0.01 USD = 1200.00 USD

>[!IMPORTANT]
> 예상 비용은 예제 용도로만 사용됩니다.  가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)