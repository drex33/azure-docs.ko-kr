---
title: Azure Lab Services를 사용하여 Project Lead the Way 랩 설정
description: Project Lead The Way 수업을 진행하도록 랩을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 9ee9c0dfdd9174691c27ee007c1dc0070249afbe
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176779"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Project Lead The Way 수업을 위한 랩 설정

[PLTW(Project Lead The Way)](https://www.pltw.org/)는 미국 전역에서 컴퓨터 과학, 엔지니어링 및 생명 공학 과학에 대한 12가지 커리큘럼에 해당하는 PreK를 제공하는 비영리 조직입니다.  각 PLTW 수업에서 학생들은 실습 교육 환경의 일부로 다양한 소프트웨어 애플리케이션을 사용합니다.  많은 소프트웨어 애플리케이션에는 고속 CPU, 경우에 따라 GPU가 필요합니다.  이 문서에서는 다음과 같은 PLTW 수업을 위한 랩 설정 방법을 보여 줍니다. 이 수업는 일반적으로 9&ndash;12학년 학생들에게 제공됩니다.

- **엔지니어링 디자인 소개**

    학생들에게는 3D 모델링을 위해 [Autodesk Inventor CAD(컴퓨터 지원 설계)](https://www.autodesk.com/products/inventor/new-features) 소프트웨어를 사용하는 방식을 포함하는 엔지니어링 디자인 프로세스가 소개되었습니다.

- **엔지니어링 원칙**
    
    학생들은 엔지니어링 메커니즘, 구조 및 재질 강도 및 자동화에 대해 알아봅니다.  이 수업에서는 [MD Solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf) 및 [America’s Army simulation](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf) 등의 소프트웨어를 사용합니다.

- **토목 공학 및 설계**

    학생들은 3D BIM(Building Information Modeling)을 위한 [Autodesk Revit](https://www.autodesk.com/products/revit/overview) 설계 디자인 소프트웨어를 사용하여 건물 및 현장 설계 및 개발을 학습합니다.

- **컴퓨터 통합 제조**

    학생들은 로봇 공학 및 자동화를 포함하는 최신 제조 공정을 살펴봅니다.   이 수업에서 학생들은 [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) 및 [Autodesk Inventor CAD(컴퓨터 지원 설계)](https://www.autodesk.com/products/inventor-cam/overview) 소프트웨어를 사용합니다. 

- **디지털 전자 제품**

    학생들은 [National Instrument Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 시뮬레이션 및 회로 설계 소프트웨어를 사용하여 전자 논리 회로 및 디바이스를 연구합니다.

- **엔지니어링 디자인 및 개발**

    학생들은 엔지니어 패널에 제공되는 연구, 디자인 및 테스트를 결합하여 엔드투엔드 솔루션에 참여합니다.  이 수업에서 학생들은 [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) 소프트웨어를 사용합니다.

- **컴퓨터 과학 기본 사항**

    학생들에게 컴퓨팅 개념 및 도구가 소개되었습니다.  블록 기반 프로그래밍부터 시작한 다음, [블록](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)과 같은 코딩 환경을 사용하여 텍스트 기반 코딩으로 이동합니다.

- **컴퓨터 과학 원칙**
    
    학생들은 [Microsoft Visual Studio Code 개발 환경](https://code.visualstudio.com/)을 사용하여 [Python](https://www.python.org/)에 대한 프로그래밍 전문 지식을 기릅니다. 

- **컴퓨터 과학 A**

    학생들은 모바일 앱 개발을 학습하여 이 수업에서 프로그래밍 역량을 강화합니다.  이 수업에서는 [Microsoft Visual Studio Code 개발 환경](https://code.visualstudio.com/)을 사용하여 [Java](https://www.java.com/)를 학습합니다.  또한 학생들은 모바일 앱 코드를 실행하고 테스트할 수 있는 에뮬레이터를 사용합니다.  Azure Lab Services에서 에뮬레이터를 설정하는 방법에 대한 자세한 내용은 [Azure Lab Services 포럼](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)을 통해 문의하세요.

전체 수업 소프트웨어 목록은 각 수업에 대한 [PLTW 사이트](https://www.pltw.org/pltw-software)로 이동합니다.

## <a name="lab-configuration"></a>랩 구성

PLTW를 위한 랩을 설정하려면 Azure 구독 및 랩 계정이 필요합니다. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. 

Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정](./tutorial-setup-lab-account.md)을 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

랩 계정을 설정한 후에는 학교에서 제공하는 각 PLTW 수업 세션에 대해 별도의 랩을 만들어야 합니다.  또한 각 PLTW 수업 유형에 대해 별도의 이미지를 만드는 것이 좋습니다.  실습 및 이미지를 구성하는 방법에 대한 자세한 내용은 블로그 게시물 [물리적 랩에서 Azure Lab Services로 이동(Moving from a Physical Lab to Azure Lab Services)](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)을 참조하세요.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다. Azure Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| -------------------- | ----- |
| Marketplace 이미지 | 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용하도록 설정합니다. |

<br>

### <a name="lab-settings"></a>랩 설정
PLTW 수업에 사용하도록 권장되는 VM(가상 머신)의 크기는 학생이 수업에서 수행하는 워크로드 유형에 따라 달라집니다.  이전에 나열된 수업의 경우 소형 GPU(시각화) 및 대형 VM 크기를 사용하는 것이 좋습니다. PLTW 수업을 위한 랩을 설정하는 경우 다음 표의 지침을 참조하세요.

| 랩 설정 | 값 및 설명 | 수업 권장 사항 |
| ------------ | ------------------ | --- |
| Virtual Machine 크기 | **소형 GPU(시각화)**<br>OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. | 이 크기는 토목 공학과 설계, 디지털 전자 제품, 컴퓨터 통합 제조, 엔지니어링 디자인 및 개발, 엔지니어링 디자인 소개 등의 PLTW 수업에 사용하는 것이 좋습니다.
| Virtual Machine 크기 | **대형**<br>더 빠른 CPU, 향상된 로컬 디스크 성능, 대형 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다. | 이 크기는 엔지니어링 원칙, 컴퓨터 과학 기본 사항, 컴퓨터 과학 원리 및 컴퓨터 과학 A 등의 PLTW 수업에 사용하는 것이 좋습니다. |

<br>

### <a name="license-server"></a>라이선스 서버
이전에 언급한 PLTW 수업에서 사용되는 대부분의 소프트웨어에는 라이선스 서버에 대한 액세스 권한이 필요하지 *않습니다*.  그러나 다음 소프트웨어에 대해 Autodesk 네트워크 라이선스 모델을 사용하려는 경우 라이선스 서버에 액세스해야 합니다.
-   Revit
-   Inventor CAD
-   Inventor CAM

Autodesk 소프트웨어에서 네트워크 라이선스를 사용하도록 하기 위해 라이선스 서버에 Autodesk Network License Manager를 설치하는 [구체적인 단계를 PLTW에서 제공](https://www.pltw.org/pltw-software)합니다.  이 라이선스 서버는 일반적으로 온-프레미스 네트워크에 있거나 Azure 가상 네트워크 내에 있는 Azure VM(가상 머신)에서 호스트됩니다.

라이선스 서버를 설정한 후에는 [랩 계정](./tutorial-setup-lab-account.md)으로 [가상 네트워크를 피어링](./how-to-connect-peer-virtual-network.md)해야 합니다. 랩 VM과 라이선스 서버가 서로 액세스할 수 있도록 랩을 만들기 ‘전에’ 네트워크 피어링을 수행해야 합니다.

Autodesk 생성 라이선스 파일은 라이선스 서버의 MAC 주소를 포함합니다.  Azure VM을 사용하여 라이선스 서버를 호스트하려면 라이선스 서버의 MAC 주소가 변경되지 않는지 확인하는 것이 중요합니다. MAC 주소가 변경되면 라이선스 파일을 다시 생성해야 합니다. MAC 주소가 변경되지 않도록 하려면 다음을 수행합니다.

- 라이선스 서버를 호스트하는 Azure VM에 대한 [정적 개인 IP 및 MAC 주소를 설정](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)합니다.
- 나중에 이러한 리소스를 새 지역 또는 위치로 이동하지 않아도 되도록 충분한 VM 용량이 있는 지역 또는 위치에서 랩 계정 및 라이선스 서버의 가상 네트워크를 설정해야 합니다.

자세한 내용은 [라이선스 서버를 공유 리소스로 설정](./how-to-create-a-lab-with-shared-resource.md)을 참조하세요.

### <a name="template-machine"></a>템플릿 머신
PLTW에 필요한 설치 파일 중 일부는 클 수 있습니다. 랩 템플릿 VM에 파일을 다운로드하는 경우 복사하는 데 시간이 오래 걸릴 수 있습니다.

설치 파일을 템플릿 머신에 다운로드하고 모든 항목을 설치하는 대신 물리적 환경에서 PLTW 이미지를 만드는 것이 좋습니다.  그러면 랩을 만들 때 사용할 수 있도록 사용자 지정 이미지를 공유 이미지 갤러리로 가져올 수 있습니다.  자세한 내용은 [사용자 지정 이미지 만들기를 위한 권장 방법](approaches-for-custom-image-creation.md)을 참조하세요.

이 권장 사항에 따라 랩 설정에 대한 주요 작업을 확인합니다.

1. 실제 환경에서 수업을 위한 이미지를 만듭니다.

    a.  설치 파일을 다운로드하고 필요한 소프트웨어를 설치하기 위한 PLTW의 자세한 단계를 사용합니다.

    > [!NOTE]    
    > Autodesk 애플리케이션을 설치하는 경우 해당 애플리케이션을 설치하는 컴퓨터가 라이선스 서버와 통신할 수 있어야 합니다. Autodesk 설치 마법사는 라이선스 서버가 호스트되는 머신의 컴퓨터 이름을 지정하라는 메시지를 표시합니다.  Azure VM에서 라이선스 서버를 호스트하는 경우 설치 마법사가 라이선스 서버에 액세스할 수 있도록 랩 템플릿 VM에 Autodesk를 설치할 때까지 기다려야 할 수 있습니다.

    b.  OneDrive 또는 학교에서 사용할 수 있는 기타 백업 옵션을 [설치 및 구성](./how-to-prepare-windows-template.md#install-and-configure-onedrive)합니다.
    
    다.  [Windows 업데이트를 설치하고 구성합니다](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  [랩 계정에 연결된 공유 이미지 갤러리](./how-to-attach-detach-shared-image-gallery.md)에 사용자 지정 이미지를 업로드합니다.

1.  랩을 만든 후 이전 단계에서 업로드한 사용자 지정 이미지를 선택합니다.

1.  랩을 만든 후 템플릿 VM을 시작하고 연결하여 이미지가 예상대로 작동하는지 확인합니다.

1.  마지막으로 템플릿 VM을 게시하여 학생의 VM을 만듭니다.

> [!NOTE]
> 학교에서 [CIPA(Children's Internet Protection Act)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act) 준수와 같은 콘텐츠 필터링을 수행해야 하는 경우 타사 소프트웨어를 사용해야 합니다.  자세한 내용은 [Lab Services를 통해 콘텐츠 필터링](./administrator-guide.md#content-filtering)에 대한 지침을 참조하세요.

## <a name="student-devices"></a>학생 디바이스
학생들은 Windows 컴퓨터, Mac 및 Chromebook에서 랩 VM에 연결할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Windows에서 연결](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac에서 연결](./connect-virtual-machine-mac-remote-desktop.md)
- [Chromebook에서 연결](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Cost
PLTW 수업에 대한 예제 비용 예상치를 살펴보겠습니다.  이러한 예상치는 라이선스 서버를 실행하거나 공유 이미지 갤러리를 사용하는 비용을 포함하지 않습니다. 25명의 학생이 수업에 참여하고 각 학생을 위해 20시간의 수업 시간이 예약됩니다.  각 학생에게는 예약된 수업 시간 외에도 과제를 위해 추가 10시간이 할당됩니다.  예상 비용은 다음과 같습니다.

- **대형 VM**

    학생 25명&times;(예약 시간 20시간 + 할당 시간 10시간) &times; 70개의 랩 유닛 &times; 시간당 USD0.01 = USD525.00

- **소형 GPU(시각화)**

    학생 25명&times;(예약 시간 20시간 + 할당 시간 10시간) &times; 160개의 랩 유닛 &times; 시간당 USD0.01 = USD1200.00

> [!IMPORTANT] 
> 예상 비용은 예제 용도로만 사용됩니다.  최신 가격 책정 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

> [!NOTE] 
> 많은 PLTW 수업에서는 MIT App Inventor와 같이 브라우저를 통해 액세스하는 애플리케이션을 사용합니다.  이러한 브라우저 기반 애플리케이션에는 빠른 CPU 또는 GPU가 필요하지 않으며 인터넷에 연결된 디바이스에서 액세스할 수 있습니다.  학생들이 이러한 유형의 애플리케이션을 사용하는 경우 랩 VM의 브라우저 대신, 실제 디바이스에서 브라우저를 사용하는 것이 좋습니다. 학생들은 빠른 CPU 또는 GPU가 필요한 애플리케이션에 대해서만 랩 VM을 사용하여 비용을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩을 설정할 때 다음 문서를 참조하세요.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users) 
