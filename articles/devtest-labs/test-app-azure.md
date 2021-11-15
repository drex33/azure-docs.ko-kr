---
title: Azure에서 앱을 테스트하는 방법
description: 데스크톱/웹 애플리케이션을 파일 공유에 배포하고 테스트하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: af9fc320c6f08e0ef4141aac8076e121a2e6292d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575944"
---
# <a name="test-your-app-in-azure"></a>Azure에서 앱 테스트 

이 가이드에서는 DevTest Labs를 사용하여 Azure에서 애플리케이션을 테스트하는 방법을 알아봅니다. Visual Studio를 사용하여 Azure 파일 공유에 앱을 배포합니다. 그런 다음, 랩 VM(가상 머신)에서 공유에 액세스합니다.  

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Visual Studio](https://visualstudio.microsoft.com/free-developer-offers/)가 로컬 워크스테이션

- [DevTest Lab](devtest-lab-overview.md)의 랩

- 랩에서 Windows가 실행되는 [Azure 가상 머신](devtest-lab-add-vm.md)

- 랩의 기존 Azure Storage 계정에 있는 [파일 공유](../storage/files/storage-how-to-create-file-share.md). 스토리지 계정은 랩에서 자동으로 만들어집니다.

- 로컬 워크스테이션 및 랩 VM에 [탑재된 Azure 파일 공유](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share)

## <a name="publish-your-app-from-visual-studio"></a>Visual Studio에서 앱 게시

이 섹션에서는 Visual Studio에서 Azure 파일 공유로 앱을 게시합니다.

1. Visual Studio를 열고 시작 창에서 **새 프로젝트 만들기** 를 선택합니다.

    :::image type="content" source="./media/test-app-in-azure/launch-visual-studio.png" alt-text="Visual Studio 시작 페이지의 스크린샷":::

1. **콘솔 애플리케이션** 을 선택하고 **다음** 을 선택합니다.

    :::image type="content" source="./media/test-app-in-azure/select-console-application.png" alt-text="콘솔 애플리케이션을 선택하는 옵션의 스크린샷":::

1. **새 프로젝트 구성** 페이지에서 기본값을 그대로 두고, **다음** 을 선택합니다.

1. **추가 정보** 페이지에서 기본값을 그대로 두고 **만들기** 를 선택합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드** 를 선택합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

    :::image type="content" source="./media/test-app-in-azure/publish-application.png" alt-text="애플리케이션을 게시하는 옵션의 스크린샷":::

1. **게시** 페이지에서 **폴더** 를 선택하고 **다음** 을 선택합니다.

    :::image type="content" source="./media/test-app-in-azure/publish-to-folder.png" alt-text="폴더에 게시하는 옵션의 스크린샷":::

1. **특정 대상** 옵션에서 **폴더** 를 선택하고 **다음** 을 선택합니다.

1. **위치** 옵션에서 **찾아보기** 를 선택하고 이전에 탑재한 파일 공유를 선택합니다. **확인** 을 선택하고 **마침** 을 선택합니다. 

    :::image type="content" source="./media/test-app-in-azure/selecting-file-share.png" alt-text="파일 공유를 선택하는 옵션의 스크린샷":::

1. **게시** 를 선택합니다. Visual Studio에서 애플리케이션이 빌드되고 파일 공유에 게시됩니다.

    :::image type="content" source="./media/test-app-in-azure/final-publish.png" alt-text="게시 단추의 스크린샷":::

## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>랩의 테스트 VM에서 앱 테스트

1. 랩 가상 머신에 연결합니다.

1. 가상 머신 내에서 **파일 탐색기** 를 시작하고 **이 PC** 를 선택하여 앞서 탑재한 파일 공유를 찾습니다.

    :::image type="content" source="./media/test-app-in-azure/find-share-on-vm.png" alt-text="파일 탐색기의 스크린샷":::

1. 파일 공유를 열고 Visual Studio에서 배포한 앱이 표시되는지 확인합니다. 

    :::image type="content" source="./media/test-app-in-azure/open-file-share.png" alt-text="파일 공유 내용의 스크린샷":::

    이제 Azure에서 만든 테스트 VM 내에서 앱에 액세스하여 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩에서 VM을 사용하는 방법을 알아보려면 다음 문서를 참조하세요. 

- [랩에 VM 추가](devtest-lab-add-vm.md)
- [랩 VM 다시 시작](devtest-lab-restart-vm.md)
- [랩 VM 크기 조정](devtest-lab-resize-vm.md)
