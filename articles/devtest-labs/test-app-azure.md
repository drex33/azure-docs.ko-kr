---
title: Azure에서 앱을 테스트하는 방법
description: 랩에서 파일 공유를 만들어 랩의 로컬 컴퓨터 및 가상 머신에 파일 공유를 탑재하는 방법을 알아본 다음, 데스크톱/웹 애플리케이션을 파일 공유에 배포하고 테스트해 보세요.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: e9d4e78bf0ebe5e28ac9bb180acafae3fcdd0f08
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604382"
---
# <a name="test-your-app-in-azure"></a>Azure에서 앱 테스트 
이 문서에서는 DevTest Lab을 사용하여 Azure에서 애플리케이션을 테스트하는 방법에 대한 단계를 제공합니다. 먼저 랩 내에서 파일 공유를 설정하고 로컬 개발 컴퓨터의 드라이브 및 랩 내의 VM으로 탑재합니다. 그러면 Visual Studio 2019를 사용하여 앱을 파일 공유에 배포하므로 랩의 VM에서 앱을 실행할 수 있습니다.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소 
1. Azure 구독이 아직 없는 경우에는 [Azure 구독을 만들고](https://azure.microsoft.com/free/)[Azure Portal](https://portal.azure.com)에 로그인합니다.
2. [이 문서](devtest-lab-create-lab.md)의 지침에 따라 Azure DevTest Labs를 사용하여 랩을 만듭니다. 나중에 로그인할 때 쉽게 찾을 수 있도록 랩을 대시보드에 고정합니다. Azure DevTest Labs를 사용하면 폐기물을 최소화하고 비용을 제어하여 Azure 내에서 리소스를 빠르게 만들 수 있습니다. DevTest Labs에 대한 자세한 내용은 [개요](devtest-lab-overview.md)를 참조하세요. 
3. [스토리지 계정 만들기](../storage/common/storage-account-create.md) 문서의 지침에 따라 랩의 리소스 그룹에 Azure Storage 계정을 만듭니다. **스토리지 계정 만들기** 페이지에서 **리소스 그룹** 에 **기존 사용** 을 선택하고 **랩 리소스 그룹** 을 선택합니다. 
4. [Azure 파일에 파일 공유 만들기](../storage/files/storage-how-to-create-file-share.md) 문서에 있는 지침에 따라 Azure Storage에서 파일 공유를 만듭니다. 

## <a name="mount-the-file-share-on-your-local-machine"></a>로컬 컴퓨터에 파일 공유 탑재
1. 로컬 머신에서는 [Windows에서 Azure 파일 공유 사용](../storage/files/storage-how-to-use-files-windows.md) 문서에 있는 [Azure 파일 공유 탑재](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) 섹션의 스크립트를 사용합니다. 
2. 그런 다음, `net use` 명령을 사용하여 파일 공유를 컴퓨터에 탑재합니다. 다음은 샘플 명령입니다. 명령을 실행하기 전에 Azure Storage 이름 및 파일 공유 이름을 지정하세요. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>랩에서 VM 만들기
1. **파일 공유** 페이지 상단의 이동 경로 탐색 메뉴에서 **리소스 그룹** 을 선택합니다. 그러면 **리소스 그룹** 페이지가 표시됩니다. 
    
    ![이동 경로 탐색 메뉴에서 리소스 그룹 선택](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. **리소스 그룹** 페이지에서 DevTest Labs에 만든 **랩** 을 선택합니다.

    ![랩 선택](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. 랩의 **DevTest Labs** 페이지에서 도구 모음의 **+ 추가** 를 선택합니다. 

    ![랩의 [추가] 단추](media/test-app-in-azure/add-button-in-lab.png)
4. **기본 선택** 페이지에서 **smalldisk** 를 검색하고 **[smalldisk] Windows Server 2016 Data Center** 를 선택합니다. 

    ![소형 디스크 Windows Server 선택](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. **가상 머신** 페이지에서 **가상 머신 이름**, **사용자 이름**, **암호** 를 지정하고 **만들기** 를 선택합니다.    
    
    ![가상 머신 만들기 페이지](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>VM에 파일 공유 탑재
1. 가상 머신이 만들어지면 목록에서 **가상 머신** 을 선택합니다.    

    ![랩 VM 선택](media/test-app-in-azure/select-lab-vm.png)
2. 도구 모음에서 **연결** 을 선택하여 VM에 연결합니다. 
3. [Azure PowerShell 설치](/powershell/azure/install-az-ps)
4. 파일 공유 탑재 섹션의 지침을 따릅니다. 

## <a name="publish-your-app-from-visual-studio"></a>Visual Studio에서 앱 게시
이 섹션에서는 Visual Studio에서 클라우드의 테스트 VM으로 앱을 게시합니다.

1. Visual Studio 2019를 사용하여 데스크톱/웹 애플리케이션을 만듭니다.
2. 앱을 빌드합니다.
3. 앱을 게시하려면 **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. 
4. **게시 마법사** 에서 파일 공유에 매핑된 **드라이브** 를 입력합니다.

    **데스크톱 앱:**

    ![데스크톱 앱](media/test-app-in-azure/desktop-app.png)

    **웹앱:**

    ![웹앱](media/test-app-in-azure/web-app.png)

1. **다음** 을 선택하여 게시 워크플로를 완료하고 **마침** 을 선택합니다. 마법사 단계를 마치면 Visual Studio에서 애플리케이션을 빌드하고 파일 공유에 게시합니다. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>랩의 테스트 VM에서 앱 테스트

1. 랩에서 VM의 가상 머신 페이지로 이동합니다. 
2. VM이 중지된 상태가 아닌 경우 도구 모음에서 **시작** 을 선택하여 VM을 시작합니다. VM의 자동 시작 및 자동 종료 정책을 설정하여 매번 시작 및 중지하지 않도록 할 수 있습니다. 
3. **연결** 을 선택합니다.

    ![가상 머신 페이지](media/test-app-in-azure/virtual-machine-page.png)
4. 가상 머신 내에서 **파일 탐색기** 를 실행하고 **이 PC** 를 선택하여 파일 공유를 찾습니다.

    ![VM에서 공유 찾기](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > 어떤 이유로든 가상 머신 또는 로컬 컴퓨터에서 파일 공유를 찾을 수 없는 경우 `net use` 명령을 실행하여 다시 탑재할 수 있습니다. Azure Portal의 **파일 공유** 에 있는 **연결** 마법사에서 `net use` 명령을 찾을 수 있습니다.
1. 파일 공유를 열고 Visual Studio에서 배포한 앱이 표시되는지 확인합니다. 

    ![VM에서 공유 열기](media/test-app-in-azure/open-file-share.png)

    이제 Azure에서 만든 테스트 VM 내에서 앱에 액세스하여 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계
랩에서 VM을 사용하는 방법을 알아보려면 다음 문서를 참조하세요. 

- [랩에 VM 추가](devtest-lab-add-vm.md)
- [랩 VM 다시 시작](devtest-lab-restart-vm.md)
- [랩 VM 크기 조정](devtest-lab-resize-vm.md)
