---
title: Azure Virtual Desktop 골든 이미지 만들기
description: Azure Portal Azure Virtual Desktop 배포에 대한 골든 이미지를 설정하는 방법에 대한 연습입니다.
author: cshea15
ms.topic: how-to
ms.date: 12/01/2021
ms.author: chashea
manager: bterkaly
ms.openlocfilehash: 02e9117c782d1636ec659245e1daab8ddc723176
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487842"
---
# <a name="create-a-golden-image-in-azure"></a>Azure에서 골든 이미지 만들기
이 문서에서는 Azure Portal 사용하여 Azure Virtual Desktop 세션 호스트에 사용할 사용자 지정 이미지를 만드는 방법을 안내합니다. "골든 이미지"라고 하는 이 사용자 지정 이미지는 배포에 적용하려는 모든 앱 및 구성 설정을 포함합니다.
세션 호스트를 사용자 지정하는 다른 방법이 있습니다. 예: [Microsoft Endpoint Manager](/mem/intune/fundamentals/azure-virtual-desktop-multi-session) 같은 디바이스 관리 도구를 사용하거나 Azure DevOps [Azure Image Builder](../virtual-machines/windows/image-builder-virtual-desktop.md) 같은 도구를 사용하여 이미지 빌드를 자동화하는 [것입니다.](/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops&preserve-view=true) 가장 잘 작동하는 전략은 계획된 Azure Virtual Desktop 환경 및 현재 애플리케이션 배포 프로세스의 복잡성과 크기에 따라 달라집니다. 
## <a name="create-an-image-from-an-azure-vm"></a>Azure VM에서 이미지 만들기
골든 이미지에 대한 새 VM을 만들 때 [지원되는 가상 머신 OS 이미지](overview.md#supported-virtual-machine-os-images)목록에 있는 OS를 선택해야 합니다.  풀된 호스트 풀에 Windows 10 다중 세션(Microsoft 365 사용 여부) 또는 Windows Server 이미지를 사용하는 것이 좋습니다. 개인 호스트 풀에 Windows 10 Enterprise 이미지를 사용하는 것이 좋습니다. 1세대 또는 2세대 VM을 사용할 수 있습니다. Gen 2 VM은 Gen 1 컴퓨터에서 지원되지 않는 기능을 지원합니다. Azure의 2세대 VM 지원에서 1세대 및 [2세대 VM에 대해](/azure/virtual-machines/generation-2.md)자세히 알아보세요.
### <a name="take-your-first-snapshot"></a>첫 번째 스냅샷 생성
먼저 선택한 이미지에 대한 [기본 VM을 만듭니다.](../virtual-machines/windows/quick-create-portal.md) 이미지를 배포한 후 이미지 VM의 디스크 스냅샷을 생성합니다. 스냅샷은 이미지를 빌드하는 동안 문제가 발생할 경우 변경 내용을 롤백할 수 있는 저장 상태입니다. 빌드 프로세스 전체에서 많은 스냅샷을 만들므로 쉽게 식별할 수 있는 이름을 스냅샷에 지정해야 합니다. 
### <a name="customize-your-vm"></a>VM 사용자 지정
VM에 로그인하고 앱, 업데이트 및 이미지에 필요한 기타 항목을 사용하여 사용자 지정을 시작합니다. 사용자 지정 중에 VM을 도메인에 가입해야 하는 경우 sysprep을 실행하기 전에 도메인에서 VM을 제거합니다. 많은 앱을 설치해야 하는 경우 문제가 발생할 경우 여러 스냅샷을 만들어 VM을 되돌리는 것이 좋습니다. 최종 스냅샷을 만들기 전에 다음 작업을 수행했는지 확인합니다.
- 최신 Windows 업데이트를 설치합니다.
- 임시 파일 정리, 디스크 조각 모음 및 불필요한 사용자 프로필 제거와 같은 필요한 정리를 완료합니다.
> [!NOTE]
> 컴퓨터에 바이러스 백신 앱이 포함되면 sysprep을 시작할 때 문제가 발생할 수 있습니다. 이를 방지하려면 sysprep을 실행하기 전에 모든 바이러스 백신 프로그램을 사용하지 않도록 설정합니다.
### <a name="take-the-final-snapshot"></a>최종 스냅샷 생성
이미지 VM에 애플리케이션 설치가 완료되면 디스크의 최종 스냅샷을 생성합니다. sysprep 또는 캡처가 실패하면 이 스냅샷에서 이미 설치된 애플리케이션을 사용하여 새 기본 VM을 만들 수 있습니다. 
### <a name="run-sysprep"></a>sysprep 실행
Sysprep을 실행하기 전에 수행할 수 있는 몇 가지 선택적 사항:
- 한 번 다시 부팅
- 시스템 스토리지에서 임시 파일 정리
- 드라이버 최적화(조각 모음)
- 사용자 프로필 제거 [sysprep](/azure/virtual-machines/generalize#windows.md)을 실행하여 VM 일반화 
 
## <a name="capture-the-vm"></a>VM 캡처
sysprep을 완료하고 Azure Portal 머신을 종료한 후 **VM** 탭을 열고 **캡처** 단추를 선택하여 나중에 사용할 수 있도록 이미지를 저장합니다. VM을 캡처할 때 공유 이미지 갤러리에 이미지를 추가하거나 관리형 이미지로 캡처할 수 있습니다.
[Shared Image Gallery](/azure/virtual-machines/shared-image-galleries.md) 사용하면 기능을 추가하고 다른 배포에서 기존 이미지를 사용할 수 있습니다. Shared Image Gallery 이미지는 고가용성이며 버전 관리가 용이하며 대규모로 배포할 수 있습니다. 그러나 더 간단한 배포가 있는 경우 독립 실행형 관리형 이미지를 대신 사용할 수 있습니다.
> [!IMPORTANT]
> 복제 및 이미지 버전과 같은 향상된 기능으로 인해 프로덕션 환경에 Shared Image Gallery 이미지를 사용하는 것이 좋습니다. 캡처를 만들 때 캡처 프로세스가 완료된 후 더 이상 사용할 수 없도록 나중에 VM을 삭제해야 합니다. 캡처에 문제가 있더라도 동일한 VM을 두 번 캡처하지 마세요. 대신 최신 스냅샷에서 새 VM을 만든 다음, sysprep을 다시 실행합니다.
캡처 프로세스가 완료되면 이미지를 사용하여 세션 호스트를 만들 수 있습니다. 이미지를 찾으려면 호스트 **풀** 탭을 열고 **갤러리** 를 선택한 다음, 모든 이미지를 선택합니다. **다음으로, 내 항목을** 선택하고 내 이미지 아래에서 관리되는 **이미지를** 찾습니다. 이미지 정의는 공유 항목 섹션 아래에 표시됩니다.
## <a name="other-recommendations"></a>기타 권장 사항
다음은 골든 이미지를 만들 때 유의해야 할 몇 가지 추가 사항입니다.
- 호스트 풀에 이미 있는 VM을 캡처하지 마세요. 이미지가 기존 VM의 구성과 충돌하고 새 VM이 작동하지 않습니다.
- sysprep을 실행하기 전에 도메인에서 VM을 제거해야 합니다. 
- 이미지를 캡처한 후 기본 VM을 삭제합니다. 
- 이미지를 캡처한 후에는 캡처한 것과 동일한 VM을 다시 사용하지 마세요. 대신, 만든 마지막 스냅샷에서 새 기본 VM을 만듭니다. 정기적으로 이 새 VM을 정기적으로 업데이트하고 패치해야 합니다.  
- 기존 사용자 지정 이미지에서 새 기본 VM을 만들지 마세요.
## <a name="next-steps"></a>다음 단계
이미지에 언어 팩을 추가하려면 언어 [팩 을 참조하세요.](language-packs.md)
