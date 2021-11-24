---
author: baanders
description: Azure Digital Twins 자습서에 대한 파일 포함 - 샘플 프로젝트에 대한 필수 구성 요소
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 27e043baf67b6f01ab66e7a5aba3d91cf143f815
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132876575"
---
## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 완료하려면 먼저 다음 필수 구성 요소를 완료해야 합니다. 

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="get-required-resources"></a>필요한 리소스 가져오기

이 자습서를 완료하려면 개발 머신에 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), **버전 16.5 이상** 을 설치합니다. 이전 버전이 이미 설치되어 있는 경우 컴퓨터에서 *Visual Studio 설치 관리자* 앱을 열고 프롬프트에 따라 설치를 업데이트할 수 있습니다.

>[!NOTE]
> Visual Studio 2019 설치에 [Azure 개발 워크로드](/dotnet/azure/configure-visual-studio) 가 포함되어 있는지 확인합니다. 이 워크로드를 통해 애플리케이션은 Azure 함수를 게시하고 다른 Azure 개발 작업을 수행할 수 있습니다.

자습서는 [C#으로 작성된 Azure Digital Twins 엔드투엔드 샘플 프로젝트](/samples/azure-samples/digital-twins-samples/digital-twins-samples)를 기반으로 합니다. 샘플 링크로 이동하여 제목 아래에서 **코드 찾아보기** 단추를 선택하여 머신에서 **샘플 프로젝트를 가져옵니다**. 그러면 **코드** 단추를 선택한 다음, **ZIP 다운로드** 를 선택하여 .zip으로 다운로드할 수 있는 샘플용 GitHub 리포지토리로 이동합니다.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="GitHub에 대한 digital-twins-samples 리포지토리의 스크린샷은 zip으로 다운로드하는 단계를 강조 표시합니다." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

그러면 .zip 폴더가 **digital-twins-samples-master.zip** 으로 머신에 다운로드됩니다. 폴더의 압축을 풀고 파일을 추출합니다.

### <a name="prepare-an-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 준비하기

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
