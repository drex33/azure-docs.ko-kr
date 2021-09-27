---
title: Azure Portal 사용하여 컨테이너 및 Blob에 대한 SAS(공유 액세스 서명) 토큰을 생성합니다.
description: Azure Portal 컨테이너 및 Blob에 대한 SAS(공유 액세스 토큰)를 생성하는 방법
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 8b97501559c7a120dad7edc472483e8d220b0357
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057363"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>스토리지 컨테이너에 대한 SAS 토큰 생성

 이 문서에서는 Azure Blob Storage 컨테이너에 대한 사용자 위임 SAS(공유 액세스 서명) 토큰을 생성하는 방법을 알아봅니다. 사용자 위임 SAS 토큰은 Azure Storage 키 대신 Azure AD(Azure Active Directory) 자격 증명으로 서명됩니다. Azure Storage 계정의 리소스에 대한 뛰어난 보안 및 위임된 액세스를 제공합니다.
높은 수준에서 작동 방식은 다음과 같습니다. 애플리케이션은 요청의 일부로 Azure Storage에 SAS 토큰을 제공합니다. 스토리지 서비스에서 SAS가 유효한지 확인하면 요청에 권한이 부여됩니다. SAS가 잘못된 것으로 간주되면 오류 코드 403(사용할 수 없음)으로 요청이 거부됩니다.

Azure Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

* **Storage** 계정은 데이터에 대해 Azure에서 고유한 네임스페이스를 제공합니다.
* **컨테이너는** 스토리지 계정에 있으며 Blob 집합을 구성합니다.
* **Blob은** 컨테이너에 있으며 텍스트 및 이진 데이터를 저장합니다.

> [!NOTE]
>
> * Azure Storage 계정이 VNet(Virtual Network) 또는 방화벽으로 보호되는 경우 SAS 토큰을 사용하여 액세스 권한을 부여할 수 없습니다. [**관리 ID를**](managed-identity-byos.md) 사용하여 스토리지 리소스에 대한 액세스 권한을 부여해야 합니다.
>
> * [**관리 ID는**](managed-identity-byos.md) 비공개 및 공개적으로 액세스할 수 있는 Azure Blob Storage 계정을 모두 지원합니다.
>

## <a name="when-to-use-a-shared-access-signature"></a>공유 액세스 서명을 사용하는 경우

* 공용 액세스 권한이 있는 스토리지 컨테이너를 사용하는 경우 SAS 토큰을 사용하여 스토리지 리소스에 대한 제한된 액세스 권한을 부여하도록 선택할 수 있습니다.

* 사용자 지정 모델을 학습하는 경우 어셈블된 학습 문서 집합을 Azure Blob Storage 컨테이너에 **업로드해야 합니다.** 사용자 위임 SAS 토큰을 사용하여 학습 리소스에 권한을 부여할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들 수**](https://azure.microsoft.com/free/) 있습니다.
* [**Form Recognizer**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 Cognitive Services [**다중 서비스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 리소스입니다.
* **표준 성능** [**Azure Blob Storage 계정.**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다. 컨테이너를 사용하여 Azure Storage 계정을 만드는 방법을 모르는 경우 다음 빠른 시작에 따라 수행합니다.

  * [**스토리지 계정 를 만듭니다.**](/azure/storage/common/storage-account-create) 스토리지 계정을 만들 때 인스턴스 세부 정보 **→** 성능 필드에서 **표준** 성능을 선택해야 합니다.
  * [**컨테이너를 만듭니다.**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) 컨테이너를 만들 때 **새** 컨테이너 창에서 **공용 액세스 수준** 필드를 **컨테이너(컨테이너** 및 Blob에 대한 익명 읽기 액세스)로 설정합니다.

## <a name="upload-your-documents"></a>문서 업로드

1. [Azure Portal](https://ms.portal.azure.com/#home) 이동하여 다음과 같이 이동합니다. **스토리지 계정** → Data **Storage** → **컨테이너**

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="스크린샷: Azure Portal 데이터 스토리지 메뉴.":::

1. 목록에서 **컨테이너를** 선택합니다.
1. 페이지 맨 위에 있는 메뉴에서 **업로드** 선택합니다.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="스크린샷: Azure Portal 컨테이너 업로드 단추":::

1. **업로드 Blob** 창이 나타납니다.
1. 업로드할 파일을 선택합니다.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="스크린샷: Azure Portal Blob 창 업로드":::

> [!NOTE]
> 기본적으로 REST API 컨테이너의 루트에 있는 양식 문서를 사용합니다. 그러나 API 호출에 지정된 경우 하위 폴더에 구성된 데이터를 사용할 수 있습니다.  [ **하위 폴더에서 데이터 구성을 참조하세요.**](/azure/applied-ai-services/form-recognizer/build-training-data-set.md#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>Azure Portal 사용하여 SAS 만들기

> [!IMPORTANT]
>
> 스토리지 계정 자체가 아닌 컨테이너에 대한 SAS를 생성하고 검색합니다.

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 다음과 같이 이동합니다.

     **스토리지 계정** → **컨테이너**
1. 목록에서 컨테이너를 선택합니다.
1. 주 창의 오른쪽으로 이동하여 선택한 컨테이너와 연결된 세 개의 타원을 선택합니다.
1. 드롭다운 메뉴에서 **SAS 생성을** 선택하여 **SAS 생성 창을** 엽니다.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="스크린샷(Azure Portal): sas 토큰 생성 드롭다운 메뉴.":::

1. **서명 방법** → **사용자 위임 키** 를 선택합니다.

1. 적절한 확인란을 선택하거나 선택 취소하여 **사용 권한을** 정의합니다. **읽기,** **쓰기,** **삭제** 및 **목록** 권한이 선택되어 있는지 확인합니다.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="스크린샷(Azure Portal): SAS 권한 필드.":::

    >[!IMPORTANT]
    >
    > * 아래와 비슷한 메시지가 수신되면 스토리지 계정의 Blob 데이터에 대한 액세스 권한을 할당해야 합니다.
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="스크린샷: 권한 부족 경고":::
    >
     > * [**Azure RBAC(Azure 역할 기반 액세스 제어)는**](/azure/role-based-access-control/overview) Azure 리소스에 대한 액세스를 관리하는 데 사용되는 권한 부여 시스템입니다. Azure RBAC를 사용하면 Azure 리소스에 대한 액세스 및 권한을 관리할 수 있습니다.
    > * [**Blob 데이터 액세스에 대한 Azure 역할 할당**](/azure/role-based-access-control/role-assignments-portal?tabs=current) 가이드에 따라 Azure Storage 컨테이너에 대한 읽기, 쓰기 및 삭제 권한을 허용하는 역할을 할당합니다(예: [**Blob 데이터 기여자 Storage).**](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)

1. 서명된 키 **시작** 및 **만료** 시간을 지정합니다. **만료 시간의 값은 SAS 시작부터 최대 7일입니다.**

1. **허용되는 IP 주소** 필드는 선택 사항으로 요청을 수락할 IP 주소 또는 IP 주소 범위를 지정합니다. 요청 IP 주소가 SAS 토큰에 지정된 IP 주소 또는 주소 범위와 일치하지 않으면 권한이 부여되지 않습니다.

1. **허용되는 프로토콜** 필드는 선택 사항으로 SAS로 만든 요청에 대해 허용되는 프로토콜을 지정합니다. 기본값은 HTTPS입니다.

1. 검토 후 **SAS 토큰 및 URL 생성** 을 선택합니다.

1. **BLOB SAS 토큰** 쿼리 문자열 및 **BLOB SAS URL** 이 창의 아래쪽 영역에 표시됩니다. **Blob SAS 토큰을** 사용하려면 스토리지 서비스 URI를 추가합니다.

1. **한 번만 표시되며 창을 닫은 후에는 검색할 수 없으므로 BLOB SAS 토큰 및 URL 값을 복사하여 안전한 위치에 붙여넣습니다.**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>Azure CLI(Command-Line 인터페이스)를 사용하여 SAS 만들기

1. Azure CLI 사용하여 컨테이너에 대한 사용자 위임 SAS를 만들려면 버전 2.0.78 이상 버전을 설치했는지 확인합니다. 설치된 버전을 확인하려면 `az --version` 명령을 사용합니다.

1. [az storage container generate-sas 명령을 호출합니다.](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas)

1. 필수 매개 변수는 다음과 같습니다.

    * `auth-mode login`. 이 매개 변수는 Azure Storage에 대한 요청이 Azure AD 자격 증명으로 권한이 부여되도록 합니다.
    * `as-user`.  이 매개 변수는 생성된 SAS가 사용자 위임 SAS임을 나타냅니다.

1. 컨테이너에서 사용자 위임 SAS에 대해 지원되는 권한에는 추가(a), 만들기(c), 삭제(d), 목록(l), 읽기(r) 및 쓰기(w)가 포함됩니다.  **r**, **w**, **d** 및 **l이** 사용 권한 매개 변수의 일부로 포함되어 있는지 확인합니다.

1. Azure CLI 사용하여 사용자 위임 SAS를 만들 때 사용자 위임 키가 유효한 최대 간격은 시작 날짜로부터 7일입니다. 따라서 시작 시간 7일 이내인 SAS의 만료 시간을 지정해야 합니다.  [ **Azure CLI 사용하여 컨테이너 또는 Blob에 대한 사용자 위임 SAS 만들기를** 참조하세요.](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a>예제

사용자 위임 SAS를 생성합니다.  대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>Blob SAS URL을 사용하는 방법

* [**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)Blob SAS URL을 사용하려면 요청 본문에 SAS URL을 추가합니다.

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* [**Form Recognizer 레이블 지정 도구에서**](https://fott-2-1.azurewebsites.net/connections/create) **Blob SAS URL을** 사용하려면 Azure **Blob 컨테이너** → SAS **URI** 필드에 → 연결 설정 **SAS** URL을 추가합니다.

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{대체 텍스트}":::

정말 간단하죠. SAS 토큰을 생성하여 클라이언트가 데이터에 액세스하는 방법을 인증하는 방법을 배웠습니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](build-training-data-set.md)
