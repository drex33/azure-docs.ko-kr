---
title: Media Services Python 기본 인코딩 빠른 시작
description: 이 빠른 시작에서는 Python 및 Azure Media Services를 사용하여 기본 인코딩을 수행하는 방법을 보여줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 7/2/2021
ms.author: inhenkel
ms.custom: mode-other
ms.openlocfilehash: 12c7628efe56b02544c3feadd6d0dfdf4d456da5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071458"
---
# <a name="media-services-basic-encoding-with-python"></a>Python을 사용한 Media Services 기본 인코딩

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>소개

이 빠른 시작에서는 Python 및 Azure Media Services를 사용하여 기본 인코딩을 수행하는 방법을 보여줍니다. 2020-05-01 Media Service v3 API를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 빠른 시작에 사용할 리소스 그룹을 만듭니다.
- Media Services v3 계정을 만듭니다.
- 스토리지 계정 키를 가져옵니다.
- 서비스 주체 및 키를 만듭니다.

## <a name="get-the-sample"></a>샘플 가져오기

포크를 만들고 [Python 샘플 리포지토리](https://github.com/Azure-Samples/media-services-v3-python)에 있는 샘플을 복제합니다. 이 빠른 시작에서는 BasicEncoding 샘플로 작업하고 있습니다.

## <a name="create-the-env-file"></a>.env 파일 만들기

계정에서 값을 가져와 *.env* 파일을 만듭니다. 이름 없이 확장만 저장하세요.  *sample.env* 를 템플릿으로 사용한 다음, *.env* 파일을 BasicEncoder 폴더에 저장합니다.

## <a name="use-python-virtual-environments"></a>Python 가상 환경 사용
샘플을 위해 다음 단계에 따라 항상 Python 가상 환경을 만들고 활성화하는 것이 좋습니다.

1. VSCode 또는 다른 편집기에서 sample 폴더를 엽니다.
2. 가상 환경 만들기

    ``` bash
      # py -3 uses the global python interpreter. You can also use python -m venv .venv.
      py -3 -m venv .venv
    ```

   이 명령은 Python venv 모듈을 실행하고 .venv라는 폴더에 가상 환경을 만듭니다.

3. 다음과 같이 가상 환경을 활성화합니다.

    ``` bash
      .venv\scripts\activate
    ```

  가상 환경은 특정 Python 인터프리터의 복사본을 격리하는 프로젝트 내의 폴더입니다. 환경을 활성화한 후(Visual Studio Code에서는 자동으로 활성화됨) pip install을 실행하면 해당 환경에만 라이브러리가 설치됩니다. 그런 다음, Python 코드를 실행하면 모든 라이브러리의 특정 버전을 사용하여 환경의 정확한 컨텍스트에서 코드가 실행됩니다. pip freeze를 실행하면 해당 라이브러리의 정확한 목록이 표시됩니다. 여러 샘플에서는 필요한 라이브러리에 대한 requirements.txt 파일을 만든 다음, pip install -r requirements.txt를 사용합니다. 요구 사항 파일은 일반적으로 Azure에 코드를 배포할 때 필요합니다.

## <a name="set-up"></a>설정

[Azure용 로컬 Python 개발 환경을 설정하고 구성](/azure/developer/python/configure-local-development-environment)합니다.

Python용 azure-identity 라이브러리를 설치합니다. 이 모듈은 Azure Active Directory 인증에 필요합니다. 자세한 내용은 [Python용 Azure ID 클라이언트 라이브러리](/python/api/overview/azure/identity-readme#environment-variables)를 참조하세요.

  ``` bash
  pip install azure-identity
  ```

[Azure Media Services](/python/api/overview/azure/media-services)용 Python SDK를 설치합니다.

최신 버전 세부 정보가 포함된 Media Services Python SDK의 Pypi 페이지는 [azure-mgmt-media](https://pypi.org/project/azure-mgmt-media/)에 있습니다.

  ``` bash
  pip install azure-mgmt-media
  ```

[Python용 Azure Storage SDK](https://pypi.org/project/azure-storage-blob/)를 설치합니다.

  ``` bash
  pip install azure-storage-blob
  ```

필요에 따라 samples 폴더에 있는 “requirements.txt” 파일을 사용하여 지정된 샘플의 모든 요구 사항을 설치할 수 있습니다.

  ``` bash
  pip install -r requirements.txt
  ```

## <a name="try-the-code"></a>코드 사용해 보기

아래 코드는 충분히 주석으로 처리되어 있습니다.  전체 스크립트를 사용하거나 일부를 자신의 스크립트에 사용합니다.

이 샘플에서는 이름을 지정하기 위해 난수가 생성되므로 스크립트를 실행할 때 함께 생성된 그룹으로 식별할 수 있습니다.  난수는 선택 사항이며 스크립트 테스트가 완료되면 제거할 수 있습니다.

이 샘플의 입력 자산에 대해 SAS URL을 사용하지 않습니다.

[!code-python[Main](../../../media-services-v3-python/BasicEncoding/basic-encoding.py)]

## <a name="delete-resources"></a>리소스 삭제

빠른 시작을 완료하면 리소스 그룹에서 만든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Media Services Python SDK](/python/api/azure-mgmt-media/)에 대해 알아보기

## <a name="resources"></a>리소스

- Azure Media Services [관리 API](/python/api/overview/azure/mediaservices/management)를 참조하세요.
- [Python에서 스토리지 API](/azure/developer/python/azure-sdk-example-storage-use?tabs=cmd)를 사용하는 방법을 알아봅니다.
- [Python용 Azure ID 클라이언트 라이브러리](/python/api/overview/azure/identity-readme#environment-variables)에 대해 자세히 알아봅니다.
- [Azure Media Services v3](./media-services-overview.md)에 대해 자세히 알아봅니다.
- [Azure Python SDK](/azure/developer/python)에 대해 알아봅니다.
- [Azure Python SDK의 사용 패턴](/azure/developer/python/azure-sdk-library-usage-patterns)에 대해 자세히 알아봅니다.
- [Azure Python SDK 인덱스](/azure/developer/python/azure-sdk-library-package-index)에서 더 많은 Azure Python SDK를 찾아봅니다.
- [Azure Storage Blob Python SDK 참조](/python/api/azure-storage-blob/)
