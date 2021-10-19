---
title: Azure Lab Services를 사용하여 Autodesk에 랩 설정
description: Autodesk를 사용하여 엔지니어링 클래스를 학습하도록 랩을 설정하는 방법을 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 2c9abda8f42023ebc54de77caadbe2860afe137f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177064"
---
# <a name="set-up-labs-for-autodesk"></a>Autodesk에 대한 랩 설정

이 문서에서는 엔지니어링 클래스에 대해 Autodesk Inventor 및 Autodesk Revit 소프트웨어를 설정하는 방법을 설명합니다.
- [Inventor 컴퓨터 지원 설계(CAD)](https://www.autodesk.com/products/inventor/new-features) 및 [컴퓨터 지원 제조(CAM)](https://www.autodesk.com/products/inventor-cam/overview)는 3D 모델링을 제공하고 엔지니어링 설계에 사용됩니다.
- [Revit](https://www.autodesk.com/products/revit/overview)은 3D 건물 정보 모델링(BIM)을 위한 아키텍처 설계에 사용됩니다.

Autodesk는 대학 및 K-12 학교에서 일반적으로 사용됩니다.  예를 들어, K-12에서 AutoDesk는 [PLTW(Project Lead the Way)](./class-type-pltw.md) 교육 과정에 포함됩니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md)의 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다. Azure Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| -------------------- | ----- |
| Marketplace 이미지 | 랩 계정 내에서 사용할 Windows 10 이미지를 사용하도록 설정합니다. |

### <a name="lab-settings"></a>랩 설정
권장하는 VM(가상 머신)의 크기는 학생이 수행해야 하는 워크로드 유형에 따라 다릅니다.  소형 GPU(시각화) 크기를 사용하는 것이 좋습니다.

| 랩 설정 | 값 및 설명 |
| ------------ | ------------------ |
| Virtual Machine 크기 | **소형 GPU(시각화)**<br>OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. | 

> [!NOTE]
> **소형 GPU(시각화)** 가상 머신 크기는 고성능 그래픽 환경을 사용하도록 구성됩니다. 이 가상 머신 크기에 대한 자세한 내용은 [GPU를 사용하여 랩을 설정하는 방법](./how-to-setup-lab-gpu.md) 관련 문서를 참조하세요.

### <a name="license-server"></a>라이선스 서버
Autodesk 네트워크 라이선스 모델을 사용하려는 경우 라이선스 서버에 액세스해야 합니다.  자세한 내용은 Autodesk의 [네트워크 라이선스 관리](https://knowledge.autodesk.com/customer-service/network-license-administration/network-deployment/preparing-for-deployment/determining-installation-type) 관련 문서를 참조하세요.

Autodesk 소프트웨어에서 네트워크 라이선스를 사용하려면 라이선스 서버에 Autodesk Network License Manager를 설치하는 [구체적인 단계를 Autodesk에서 제공](https://knowledge.autodesk.com/customer-service/network-license-administration/install-and-configure-network-license)합니다.  이 라이선스 서버는 일반적으로 온-프레미스 네트워크에 있거나 Azure 가상 네트워크 내에 있는 Azure 가상 머신(VM)에서 호스트됩니다.

라이선스 서버를 설정한 후에는 [랩 계정](./tutorial-setup-lab-account.md)으로 [가상 네트워크를 피어링](./how-to-connect-peer-virtual-network.md)해야 합니다. 랩 VM과 라이선스 서버가 서로 액세스할 수 있도록 랩을 만들기 ‘전에’ 네트워크 피어링을 수행해야 합니다.

Autodesk 생성 라이선스 파일은 라이선스 서버의 MAC 주소를 포함합니다.  Azure VM을 사용하여 라이선스 서버를 호스트하려면 라이선스 서버의 MAC 주소가 변경되지 않는지 확인하는 것이 중요합니다. MAC 주소가 변경되면 라이선스 파일을 다시 생성해야 합니다. MAC 주소가 변경되지 않도록 하려면 다음을 수행합니다.

- 라이선스 서버를 호스트하는 Azure VM에 대한 [정적 개인 IP 및 MAC 주소를 설정](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)합니다.
- 나중에 이러한 리소스를 새 지역 또는 위치로 이동하지 않아도 되도록 충분한 VM 용량이 있는 지역 또는 위치에서 랩 계정 및 라이선스 서버의 가상 네트워크를 설정해야 합니다.

자세한 내용은 [라이선스 서버를 공유 리소스로 설정](./how-to-create-a-lab-with-shared-resource.md)을 참조하세요.

> [!WARNING]
> 랩을 만들기 **전에** 라이선스 서버의 가상 네트워크에 랩 계정의 [가상 네트워크를 피어링](./how-to-connect-peer-virtual-network.md)하는 것을 잊지 마세요.

### <a name="template-machine"></a>템플릿 머신
이 섹션의 단계에서는 템플릿 VM을 설정하는 방법을 보여 줍니다.

1. 템플릿 VM을 시작하고 컴퓨터에 연결합니다.

1. [AutoDesk의 지침](https://knowledge.autodesk.com/customer-service/download-install/install-software)을 사용하여 Inventor 및 Revit을 다운로드하고 설치합니다.  메시지가 표시되면 라이선스 서버의 컴퓨터 이름을 지정합니다.

1.  마지막으로 템플릿 VM을 게시하여 학생의 VM을 만듭니다.

## <a name="cost"></a>Cost
이 수업의 예상 비용 예시를 살펴보겠습니다.  이 예상치에는 라이선스 서버 실행 비용은 포함되지 않습니다. 25명의 학생이 수업에 참여하고 각 학생을 위해 20시간의 수업 시간이 예약됩니다.  각 학생에게는 예약된 수업 시간 외에도 과제를 위해 추가 10시간이 할당됩니다.  선택한 가상 머신 크기는 **소형 GPU(시각화)** 로, 랩 단위는 160개입니다.

- 학생 25명&times;(예약 시간 20시간 + 할당 시간 10시간) &times; 160개의 랩 유닛 &times; 시간당 USD0.01 = USD1200.00

> [!IMPORTANT] 
> 예상 비용은 예제 용도로만 사용됩니다.  최신 가격 책정 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

랩을 설정할 때 다음 문서를 참조하세요.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users) 
