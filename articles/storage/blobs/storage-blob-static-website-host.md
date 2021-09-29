---
title: '자습서: Blob 스토리지에서 정적 웹 사이트 호스팅 - Azure Storage'
description: 정적 웹 사이트를 호스팅하기 위해 스토리지 계정을 구성하고 Azure Storage에 정적 웹 사이트를 배포하는 방법을 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: f222475560c282e7eb63c55a3f827f8f8e48c8ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549474"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>자습서: Blob Storage에서 정적 웹 사이트 호스팅

이 자습서에서는 정적 웹 사이트를 빌드하고 Azure Storage에 배포하는 방법을 알아봅니다. 작업을 완료하면 사용자가 공개적으로 액세스할 수 있는 정적 웹 사이트가 생성됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> - 정적 웹 사이트 호스팅 구성
> - Hello World 웹 사이트 배포

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE]
> 이제 범용 v2 Standard 스토리지 계정뿐만 아니라 계층적 네임스페이스가 활성화된 스토리지 계정에 정적 웹 사이트를 사용할 수 있습니다.

이 자습서에서는 프로그래머를 위한 무료 도구인 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하여 정적 웹 사이트를 빌드하고 Azure Storage 계정에 배포합니다.

Visual Studio Code를 설치한 후에 Azure Storage 미리 보기 확장을 설치합니다. 이 확장은 Visual Studio Code와 Azure Storage 관리 기능을 통합합니다. 확장을 사용하여 Azure Storage에 정적 웹 사이트를 배포합니다. 확장을 설치하려면:

1. Visual Studio Code를 시작합니다.
2. 도구 모음에서 **확장** 을 클릭합니다. *Azure Storage* 를 검색하고 목록에서 **Azure Storage** 확장을 선택합니다. 그런 다음, **설치** 단추를 클릭하여 확장을 설치합니다.

    ![VS Code에서 Azure Storage 확장 설치](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

## <a name="configure-static-website-hosting"></a>정적 웹 사이트 호스팅 구성

첫 번째 단계는 Azure Portal에서 정적 웹 사이트를 호스트하도록 스토리지 계정을 구성하는 것입니다. 정적 웹 사이트를 호스팅하기 위해 계정을 구성하는 경우 Azure Storage에서는 *$web* 이라는 컨테이너를 자동으로 만듭니다. *$web* 컨테이너에는 정적 웹 사이트에 대한 파일이 포함됩니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)을 엽니다.
1. 스토리지 계정을 찾아 계정 개요를 표시합니다.
1. **정적 웹 사이트** 를 선택하여 정적 웹 사이트에 대한 구성 페이지를 표시합니다.
1. **사용** 을 선택하여 스토리지 계정에서 정적 웹 사이트를 호스팅할 수 있습니다.
1. **인덱스 문서 이름** 필드에서 *index.html* 이라는 기본 인덱스 페이지를 지정합니다. 기본 인덱스 페이지는 사용자가 정적 웹 사이트의 루트로 이동할 때 표시됩니다.
1. **오류 문서 이름** 필드에서 *404.html* 이라는 기본 오류 페이지를 지정합니다. 기본 오류 페이지는 사용자가 정적 웹 사이트에서 존재하지 않는 페이지로 이동하려고 할 때 표시됩니다.
1. **저장** 을 클릭합니다. 이제 Azure Portal에는 정적 웹 사이트 엔드포인트가 표시됩니다.

    ![스토리지 계정에서 정적 웹 사이트 호스팅 설정](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Hello World 웹 사이트 배포

다음으로, Visual Studio Code를 사용하여 Hello World 웹 페이지를 만들고 Azure Storage 계정에서 호스트되는 정적 웹 사이트에 배포합니다.

1. 로컬 파일 시스템에 *mywebsite* 라는 빈 폴더를 만듭니다.
1. Visual Studio Code를 시작하고, **탐색기** 패널에서 방금 만든 폴더를 엽니다.

    ![Visual Studio Code에서 폴더 열기](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. *mywebsite* 폴더에서 기본 인덱스 파일을 만들고 이름을 *index.html* 이라고 지정합니다.

    ![Visual Studio Code에서 기본 인덱스 파일 만들기](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. 편집기에서 *index.html* 을 열고 파일에 다음 텍스트를 붙여넣고 저장합니다.

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. 기본 오류 파일을 만들고 이름을 *404 html* 이라고 지정합니다.
1. 편집기에서 *404.html* 을 열어서 다음 텍스트를 파일에 붙여넣고 저장합니다.

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. **탐색기** 패널의 *mywebsite* 폴더 아래를 마우스 오른쪽 단추로 클릭하고 **정적 웹 사이트에 배포...** 를 선택하여 웹 사이트를 배포합니다. 구독 목록을 검색하려면 Azure에 로그인하라는 메시지가 표시됩니다.

1. 정적 웹 사이트를 호스팅하도록 설정한 스토리지 계정에 포함된 구독을 선택합니다. 다음으로, 메시지가 표시되면 스토리지 계정을 선택합니다.

이제 Visual Studio Code에서는 웹 엔드포인트에 파일을 업로드하고 성공 상태 표시줄을 표시하게 됩니다. 웹 사이트를 시작하여 Azure에서 확인합니다.

이 자습서를 완료하고 정적 웹 사이트를 Azure에 배포했습니다.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 정적 웹 사이트를 호스팅하기 위해 Azure Storage 계정을 구성하는 방법 및 정적 웹 사이트를 만들고 Azure 엔드포인트에 배포하는 방법을 알아보았습니다.

다음으로 정적 웹 사이트에서 사용자 지정 도메인을 구성하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)
