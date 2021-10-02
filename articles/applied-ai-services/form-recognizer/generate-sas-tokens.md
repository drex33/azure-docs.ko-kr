---
title: Azure Portal를 사용 하 여 컨테이너 및 blob에 대 한 SAS (공유 액세스 서명) 토큰을 생성 합니다.
description: Azure Portal 컨테이너 및 blob에 대 한 SAS (공유 액세스 토큰)를 생성 하는 방법
ms.topic: how-to
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.date: 09/23/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 6e1b145c5efa9135a198cd6623c450f5965a6c2c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387946"
---
# <a name="generate-sas-tokens-for-storage-containers"></a>저장소 컨테이너에 대 한 SAS 토큰 생성

 이 문서에서는 Azure Blob Storage 컨테이너에 대 한 사용자 위임 SAS (공유 액세스 서명) 토큰을 생성 하는 방법을 알아봅니다. 사용자 위임 SAS 토큰은 azure storage 키 대신 Azure Active Directory (azure AD) 자격 증명으로 서명 됩니다. Azure storage 계정에서 리소스에 대 한 보다 안전 하 고 위임 된 액세스를 제공 합니다.
높은 수준에서 작동 하는 방법은 응용 프로그램에서 요청의 일부로 Azure storage에 SAS 토큰을 제공 하는 것입니다. 저장소 서비스에서 SAS가 유효한 지 확인 하는 경우 요청에 권한이 부여 됩니다. SAS가 잘못 된 것으로 간주 되 면 요청이 거부 되 고 오류 코드 403 (사용할 수 없음)이 발생 합니다.

Azure blob storage는 세 가지 유형의 리소스를 제공 합니다.

* **Storage** 계정은 데이터에 대해 Azure에서 고유한 네임 스페이스를 제공 합니다.
* **컨테이너** 는 저장소 계정에 있으며 blob 집합을 구성 합니다.
* **Blob** 은 컨테이너에 있으며 텍스트와 이진 데이터를 저장 합니다.

> [!NOTE]
>
> * Azure storage 계정이 Virtual Network (VNet) 또는 방화벽으로 보호 되는 경우 SAS 토큰을 사용 하 여 액세스 권한을 부여할 수 없습니다. [**관리 id**](managed-identity-byos.md) 를 사용 하 여 저장소 리소스에 대 한 액세스 권한을 부여 해야 합니다.
>
> * [**관리 Id는**](managed-identity-byos.md) 개인적이 고 공개적으로 액세스 가능한 Azure blob storage 계정을 모두 지원 합니다.
>

## <a name="when-to-use-a-shared-access-signature"></a>공유 액세스 서명을 사용하는 경우

* 공용 액세스로 저장소 컨테이너를 사용 하는 경우 SAS 토큰을 사용 하 여 저장소 리소스에 대 한 제한 된 액세스 권한을 부여할 수 있습니다.

* 사용자 지정 모델을 학습 하는 경우 어셈블된 학습 문서 집합을 Azure blob 저장소 컨테이너에 업로드 **해야 합니다** . 사용자 위임 SAS 토큰을 사용 하 여 학습 리소스에 대 한 사용 권한을 부여할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들 수**](https://azure.microsoft.com/free/) 있습니다.
* [**폼 인식기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) 또는 [**Cognitive Services 다중 서비스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) 리소스입니다.
* **표준 성능** [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). 스토리지 계정 내에서 Blob 데이터를 저장하고 구성하는 컨테이너를 만듭니다. 컨테이너를 사용 하 여 Azure storage 계정을 만드는 방법을 모를 경우 다음 빠른 시작을 따르세요.

  * [**저장소 계정을 만듭니다**](/azure/storage/common/storage-account-create). 저장소 계정을 만들 때 **인스턴스 세부 정보 → 성능** 필드에서 **표준** 성능을 선택 해야 합니다.
  * [**컨테이너를 만듭니다**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container). 컨테이너를 만들 때 **새 컨테이너** 창에서 **공용 액세스 수준** 필드를 **컨테이너** (컨테이너 및 blob에 대 한 익명 읽기 권한)로 설정 합니다.

## <a name="upload-your-documents"></a>문서 업로드

1. [Azure Portal](https://ms.portal.azure.com/#home) 로 이동 하 여 **저장소 계정** → **데이터 저장소** → **컨테이너** 와 같이 이동 합니다.

   :::image type="content" source="media/sas-tokens/data-storage-menu.png" alt-text="스크린샷: Azure Portal의 데이터 저장소 메뉴":::

1. 목록에서 **컨테이너** 를 선택 합니다.
1. 페이지 맨 위에 있는 메뉴에서 **업로드** 를 선택 합니다.

    :::image type="content" source="media/sas-tokens/container-upload-button.png" alt-text="스크린샷: Azure Portal의 컨테이너 업로드 단추":::

1. **업로드 blob** 창이 표시 됩니다.
1. 업로드할 파일을 선택 합니다.

    :::image type="content" source="media/sas-tokens/upload-blob-window.png" alt-text="스크린샷: Azure Portal에서 blob을 업로드 합니다.":::

> [!NOTE]
> 기본적으로 REST API는 컨테이너의 루트에 있는 양식 문서를 사용 합니다. 그러나 API 호출에 지정 된 경우 하위 폴더에 구성 된 데이터를 사용할 수 있습니다.  [ **하위 폴더의 데이터 구성** 을 참조 하세요.](/azure/applied-ai-services/form-recognizer/build-training-data-set#organize-your-data-in-subfolders-optional)

## <a name="create-a-sas-with-the-azure-portal"></a>Azure Portal를 사용 하 여 SAS 만들기

> [!IMPORTANT]
>
> 저장소 계정 자체가 아니라 컨테이너에 대 한 SAS를 생성 하 고 검색 합니다.

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 다음과 같이 이동 합니다.

     **저장소 계정** → **컨테이너**
1. 목록에서 컨테이너를 선택 합니다.
1. 주 창의 오른쪽으로 이동 하 여 선택한 컨테이너와 연결 된 세 개의 줄임표를 선택 합니다.
1. 드롭다운 메뉴에서 **Sas 생성** 을 선택 하 여 **sas 생성 창을** 엽니다.

    :::image type="content" source="media/sas-tokens/generate-sas.png" alt-text="스크린샷 (Azure Portal): sas 토큰 생성 드롭다운 메뉴":::

1. **서명 방법** → **사용자 위임 키** 를 선택합니다.

1. 적절 한 확인란을 선택 하거나 선택 취소 하 여 **사용 권한을** 정의 합니다. **읽기**, **쓰기**, **삭제** 및 **나열** 권한이 선택 되어 있는지 확인 합니다.

    :::image type="content" source="media/sas-tokens/sas-permissions.png" alt-text="스크린샷 (Azure Portal): SAS 권한 필드입니다.":::

    >[!IMPORTANT]
    >
    > * 아래와 유사한 메시지가 표시 되 면 저장소 계정의 blob 데이터에 대 한 액세스 권한을 할당 해야 합니다.
    >
    >     :::image type="content" source="media/sas-tokens/need-permissions.png" alt-text="스크린샷: 사용 권한 경고가 부족 합니다.":::
    >
     > * Azure RBAC ( [**역할 기반 액세스 제어**](/azure/role-based-access-control/overview) )는 azure 리소스에 대 한 액세스를 관리 하는 데 사용 되는 권한 부여 시스템입니다. Azure RBAC를 사용 하 여 Azure 리소스에 대 한 액세스 및 권한을 관리할 수 있습니다.
    > * [**blob 데이터에 대 한 액세스를 위한 azure 역할 할당**](/azure/role-based-access-control/role-assignments-portal?tabs=current) 가이드에 따라 azure storage 컨테이너에 대 한 읽기, 쓰기 및 삭제 권한을 허용 하는 역할을 할당 합니다. 예를 들어 [**blob 데이터 참가자를 Storage**](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)합니다.

1. 서명된 키 **시작** 및 **만료** 시간을 지정합니다. **만료 시간에 대 한 값은 SAS 시작부터 최대 7 일** 입니다.

1. **허용되는 IP 주소** 필드는 선택 사항으로 요청을 수락할 IP 주소 또는 IP 주소 범위를 지정합니다. 요청 IP 주소가 SAS 토큰에 지정된 IP 주소 또는 주소 범위와 일치하지 않으면 권한이 부여되지 않습니다.

1. **허용되는 프로토콜** 필드는 선택 사항으로 SAS로 만든 요청에 대해 허용되는 프로토콜을 지정합니다. 기본값은 HTTPS입니다.

1. 검토 후 **SAS 토큰 및 URL 생성** 을 선택합니다.

1. **BLOB SAS 토큰** 쿼리 문자열 및 **BLOB SAS URL** 이 창의 아래쪽 영역에 표시됩니다. **BLOB SAS 토큰** 을 사용 하려면 저장소 서비스 URI를 추가 합니다.

1. **한 번만 표시되며 창을 닫은 후에는 검색할 수 없으므로 BLOB SAS 토큰 및 URL 값을 복사하여 안전한 위치에 붙여넣습니다.**

## <a name="create-a-sas-with-azure-command-line-interface-cli"></a>Azure CLI (Command-Line 인터페이스)를 사용 하 여 SAS 만들기

1. Azure CLI를 사용 하 여 컨테이너에 대 한 사용자 위임 SAS를 만들려면 버전 2.0.78 이상을 설치 했는지 확인 합니다. 설치된 버전을 확인하려면 `az --version` 명령을 사용합니다.

1. [Az storage container generate sas](/cli/azure/storage/container?view=azure-cli-latest&preserve-view=true#az_storage_container_generate_sas) 명령을 호출 합니다.

1. 필수 매개 변수는 다음과 같습니다.

    * `auth-mode login`. 이 매개 변수를 사용 하면 azure storage에 대 한 요청이 Azure AD 자격 증명으로 인증 됩니다.
    * `as-user`.  이 매개 변수는 생성 된 SAS가 사용자 위임 SAS 임을 나타냅니다.

1. 컨테이너에서 사용자 위임 SAS에 대해 지원 되는 권한에는 추가 (a), 만들기 (c), 삭제 (d), 목록 (l), 읽기 (r) 및 쓰기 (w)가 있습니다.  **R**, **w**, **d** 및 **l** 이 사용 권한 매개 변수의 일부로 포함 되어 있는지 확인 합니다.

1. Azure CLI를 사용 하 여 사용자 위임 SAS를 만들 때 사용자 위임 키가 유효한 최대 간격은 시작 날짜 로부터 7 일입니다. 따라서 시작 시간 7 일 이내에 SAS의 만료 시간을 지정 해야 합니다.  [ **Azure CLI 사용 하 여 컨테이너 또는 blob에 대 한 사용자 위임 SAS 만들기** 를 참조 하세요.](/azure/storage/blobs/storage-blob-user-delegation-sas-create-cli#use-azure-ad-credentials-to-secure-a-sas)

### <a name="example"></a>예제

사용자 위임 SAS를 생성 합니다.  대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions rwdl \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

## <a name="how-to-use-your-blob-sas-url"></a>Blob SAS URL을 사용 하는 방법

* [**REST API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)에서 BLOB sas url을 사용 하려면 sas url을 요청 본문에 추가 합니다.

  ```json
  {
      "source":"<BLOB SAS URL>"
  }
  ```

* [**양식 인식기 레이블 지정 도구**](https://fott-2-1.azurewebsites.net/connections/create)에서 **Blob sas url** 을 사용 하려면 **연결 설정** → **Azure Blob 컨테이너** → **sas URI** 필드에 sas url을 추가 합니다.

  :::image type="content" source="media/sas-tokens/fott-add-sas-uri.png" alt-text="{대체 텍스트}":::

정말 간단하죠. 클라이언트에서 데이터에 액세스 하는 방법에 대 한 권한을 부여 하기 위해 SAS 토큰을 생성 하는 방법을 배웠습니다.

> [!div class="nextstepaction"]
> [학습 데이터 세트 빌드](build-training-data-set.md)
