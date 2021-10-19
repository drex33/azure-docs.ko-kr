---
title: Azure Lab Services를 사용하여 Adobe Creative Cloud로 랩 설정 | Microsoft Docs
description: Adobe Creative Cloud를 사용하는 디지털 아트 및 미디어 클래스를 위한 랩을 설정하는 방법에 대해 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 3802687b76929bda96e34a9b1cb402096bee40c5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177159"
---
# <a name="set-up-a-lab-for-adobe-creative-cloud"></a>Adobe Creative Cloud에 대한 랩 설정
[Adobe Creative Cloud](https://www.adobe.com/creativecloud.html)는 사진, 디자인, 비디오, 웹, UX(사용자 환경) 등에 사용되는 데스크톱 애플리케이션 및 웹 서비스의 컬렉션입니다.  대학 및 K-12 학교는 디지털 아트 및 미디어 클래스에서 Creative Cloud를 사용합니다.  일부 Creative Cloud의 미디어 프로세스에는 일반적인 태블릿, 랩톱 또는 워크스테이션 지원보다 더 많은 계산 및 시각화(GPU) 전력이 필요할 수 있습니다.  Azure Lab Services를 사용하여 GPU 크기를 비롯한 다양한 VM(가상 머신) 크기에서 유연하게 선택할 수 있습니다.

이 문서에서는 Creative Cloud를 사용하는 클래스를 설정하는 방법을 보여 줍니다.

## <a name="licensing"></a>라이선스
랩 VM에서 Creative Cloud를 사용하려면 가상 머신에서 배포를 지원하는 유일한 유형의 라이선스인 [명명된 사용자 라이선스](https://helpx.adobe.com/enterprise/kb/technical-support-boundaries-virtualized-server-based.html#main_Licensing_considerations)를 사용해야 합니다.  각 랩 VM은 인터넷에 액세스할 수 있으므로 학생들이 소프트웨어에 로그인하여 Creative Cloud 앱을 활성화할 수 있습니다.  학생의 로그인이 완료되면 해당 인증 토큰이 사용자 프로필에 캐시되므로 VM에서 다시 로그인할 필요가 없습니다.  자세한 내용은 [라이선스에 대한 Adobe의 문서](https://helpx.adobe.com/enterprise/using/licensing.html)를 참조하세요.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md) 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지를 지정하는 방법](./specify-marketplace-images.md)에 대한 문서를 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용할 Windows 10 이미지를 사용하도록 설정합니다.|

### <a name="lab-settings"></a>랩 설정

랩에 사용해야 하는 VM의 크기는 학생들이 만들 프로젝트의 유형에 따라 달라집니다.  대부분의 [Creative Cloud 앱](https://helpx.adobe.com/creative-cloud/system-requirements.html)은 GPU 기반 가속을 지원하며 기능이 제대로 작동하려면 GPU가 필요합니다.  적절한 VM 크기를 선택하려면 적절한 성능을 보장하기 위해 학생들이 만드는 프로젝트를 테스트하는 것이 좋습니다.  아래 표에서는 Creative Cloud에 사용하기 위해 권장되는 [VM 크기](./administrator-guide.md#vm-sizing)를 보여 줍니다.  

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| **소형 GPU(시각화)**  이 VM은 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다.|  
|가상 머신 이미지| Windows 10 |

> [!NOTE]
> **소형 GPU(시각화)** 가상 머신 크기는 고성능 그래픽 환경을 지원하고 [각 애플리케이션에 대한 Adobe의 시스템 요구 사항](https://helpx.adobe.com/creative-cloud/system-requirements.html)을 충족할 수 있도록 구성됩니다.  소형 GPU(시각화)가 아닌 소형 GPU(컴퓨팅)를 선택해야 합니다.  이 가상 머신 크기에 대한 자세한 내용은 [GPU를 사용하여 랩을 설정하는 방법](./how-to-setup-lab-gpu.md) 관련 문서를 참조하세요.

## <a name="template-virtual-machine-configuration"></a>템플릿 가상 머신 구성

### <a name="creative-cloud-deployment-package"></a>Creative Cloud 배포 패키지
Creative Cloud를 설치하려면 배포 패키지를 사용해야 합니다. 일반적으로 배포 패키지는 Adobe의 관리 콘솔을 사용하여 IT 부서에서 만듭니다.  IT에서 배포 패키지를 만들 때 셀프 서비스를 사용하도록 설정하는 옵션도 있습니다.  배포 패키지에 대해 셀프 서비스를 사용하도록 설정하는 몇 가지 방법이 있습니다.
-    셀프 서비스 패키지를 만듭니다.
-    셀프 서비스 승격된 권한이 설정된 상태에서 관리형 패키지를 만듭니다.

셀프 서비스를 사용하도록 설정하면 앱의 전체 Creative Cloud 컬렉션을 설치하지 않습니다.  대신 학생들은 Creative Cloud 데스크톱 앱을 사용하여 앱을 직접 설치할 수 있습니다.  이 접근 방식의 몇 가지 주요 이점은 다음과 같습니다.
- 전체 Creative Cloud 설치는 약 25GB입니다.  학생들이 필요할 때 필요한 앱만 설치하면 디스크 공간을 최적화하는 데 도움이 됩니다. 랩 VM의 디스크 크기는 128GB입니다.
- 게시하기 전에 템플릿 VM에 앱의 하위 집합을 설치하도록 선택할 수 있습니다.  이러한 방식으로 학생 VM은 기본적으로 일부 앱을 설치하고 학생들은 필요에 따라 앱을 직접 추가할 수 있습니다.
- 학생이 랩의 수명 동안 언제든지 VM에 추가 앱을 설치할 수 있기 때문에 템플릿 VM을 다시 게시하지 않을 수 있습니다.  그렇지 않으면 IT 또는 교사는 템플릿 VM에 추가 앱을 설치하고 다시 게시해야 합니다.  재게시로 인해 학생의 VM이 다시 설정되고 외부적으로 저장되지 않은 모든 작업이 손실됩니다.

셀프 서비스를 사용할 수 없는 관리형 배포 패키지를 사용하는 경우 학생들은 자신의 앱을 설치할 수 없습니다.  이 경우 IT는 설치될 Creative Cloud 앱을 지정해야 합니다.

자세한 내용은 [패키지를 만드는 Adobe의 단계](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/create-nul-packages.ug.html)를 참조하세요.

### <a name="install-creative-cloud"></a>Creative Cloud 설치
템플릿 머신을 만든 후 다음 단계에 따라 Creative Cloud를 사용하여 랩의 템플릿 VM(가상 머신)을 설정합니다.
1. 템플릿 VM을 시작하고 RDP를 사용하여 연결합니다.
1. Creative Cloud를 설치하려면 IT에서 해당 사용자에게 제공되는 배포 패키지를 다운로드하거나 [Adobe의 관리 콘솔](https://adminconsole.adobe.com/)에서 직접 다운로드합니다.
1. 배포 패키지 파일을 실행합니다.  셀프 서비스가 사용하도록 설정되었는지 여부에 따라 Creative Cloud 데스크톱 앱 및/또는 지정된 Creative Cloud 앱이 설치됩니다.
자세한 내용은 [Adobe의 배포 단계](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/deploy-packages.ug.html)를 참조하세요.
1. 템플릿 VM이 설정되면 랩에서 모든 학생의 VM을 만드는 데 사용되는 [템플릿 VM의 이미지를 게시](how-to-create-manage-template.md)합니다.

### <a name="storage"></a>스토리지
앞서 언급했듯이 Azure Lab VM의 디스크 크기는 128GB입니다.  학생에게 용량이 많은 미디어 자산을 저장하기 위한 추가 스토리지가 필요하거나 공유 미디어 자산에 액세스해야 하는 경우 외부 파일 스토리지 사용을 고려해야 합니다.  자세한 내용은 다음 문서를 읽어보세요.
-    [Lab Services에서 외부 파일 스토리지 사용](how-to-attach-external-storage.md)
-    [OneDrive 설치 및 구성](./how-to-prepare-windows-template.md#install-and-configure-onedrive)

### <a name="save-template-vm-image"></a>템플릿 VM 이미지 저장
나중에 사용하기 위해 템플릿 VM을 저장하는 것이 좋습니다.  템플릿 VM을 저장하려면 [Shared Image Gallery에 이미지 저장](./how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)을 참조하세요.
- 셀프 서비스를 *사용하도록 설정한* 경우 템플릿 VM의 이미지에 Creative Cloud 데스크톱이 설치됩니다.  그러면 교사는 이 이미지를 다시 사용하여 랩을 만들고 설치할 Creative Cloud 앱을 선택할 수 있습니다.  이를 통해 교사는 개별적으로 랩을 설정하고 해당 클래스에 필요한 Creative Cloud 앱을 설치하는 기능을 완벽하게 제어할 수 있으므로 IT 오버헤드를 줄일 수 있습니다.
- 셀프 서비스를 *사용하지 않도록 설정한* 경우 템플릿 VM의 이미지에는 지정된 Creative Cloud 앱이 이미 설치되어 있습니다.  교사는 이 이미지를 다시 사용하여 랩을 만들 수 있지만 추가 Creative Cloud 앱을 설치할 수는 없습니다.

## <a name="cost"></a>비용

이 섹션에서는 이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다.  20시간의 예약된 수업 시간에 25명의 학생을 사용합니다.  예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다. 선택한 가상 머신 크기는 **소형 GPU(시각화)** 로, 랩 단위는 160개입니다.

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