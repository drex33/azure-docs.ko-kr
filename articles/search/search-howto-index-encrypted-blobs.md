---
title: 암호화된 Azure Blob Storage 콘텐츠 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search를 사용하는 Azure Blob Storage에서 암호화된 문서의 텍스트를 인덱싱하고 추출하는 방법을 알아봅니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: 77faf0d946bdd499f37e83c769c6561f73f1bb54
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869101"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Azure Cognitive Search에서 BLOB 인덱서 및 기술 세트를 사용하여 암호화된 BLOB을 인덱싱하는 방법

이 문서에서는 [Azure Cognitive Search](search-what-is-azure-search.md)를 통해 [Azure Key Vault](../key-vault/general/overview.md)를 사용하는 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 내에서 이전에 암호화된 문서를 인덱싱하는 방법을 보여 줍니다. 일반적으로 인덱서는 암호화 키에 대한 액세스 권한이 없기 때문에 암호화된 파일에서 콘텐츠를 추출할 수 없습니다. 그러나 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile) 사용자 지정 기술, [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)을 활용하여 키에 대한 제어된 액세스를 제공하여 파일의 암호를 해독한 다음 해당 파일에서 콘텐츠를 추출할 수 있습니다. 이렇게 하면 저장된 문서의 암호화 상태를 손상시키지 않고 이러한 문서를 인덱싱하는 기능이 해제됩니다.

Azure Blob Storage에서 PDF, HTML, DOCX 및 PPTX와 같은 이전에 암호화된 전체 문서(구조화되지 않은 텍스트)부터 이 가이드는 Postman 및 Search REST API를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 문서의 암호를 해독하고 텍스트를 추출하는 파이프라인을 정의합니다.
> * 출력을 저장할 인덱스를 정의합니다.
> * 파이프라인을 실행하여 인덱스를 만들고 로드합니다.
> * 전체 텍스트 검색과 풍부한 쿼리 구문을 사용하여 결과를 검색합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 엽니다.

## <a name="prerequisites"></a>필수 구성 요소

이 예에서는 Azure Blob Storage에 파일을 이미 업로드하고 프로세스에서 암호화한 것으로 가정합니다. 처음에 파일을 업로드하고 암호화하는 데 도움이 필요한 경우 [이 자습서](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)를 통해 이 작업을 수행하는 방법을 확인하세요.

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ Azure Cognitive Search와 동일한 구독의 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 가 설정되어 있어야 합니다.
+ [청구 가능한 계층](search-sku-tier.md#tier-descriptions)의 [Azure Cognitive Search](search-create-service-portal.md)(기본 이상, 모든 지역)
+ [Azure 함수](https://azure.microsoft.com/services/functions/)
+ [Postman 데스크톱 앱](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 - 서비스 만들기 및 자격 증명 수집

### <a name="set-up-the-custom-skill"></a>사용자 지정 기술 설정

이 예제에서는 [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 리포지토리의 샘플 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile) 프로젝트를 사용합니다. 이 섹션에서는 기술 세트에 사용할 수 있도록 기술을 Azure 함수에 배포합니다. 기본 제공 배포 스크립트는 **psdbf-function-app-** 으로 시작하는 Azure 함수 리소스를 만들고 해당 기술을 로드합니다. 구독 및 리소스 그룹을 제공하라는 메시지가 표시됩니다. Azure Key Vault 인스턴스가 상주하는 것과 동일한 구독을 선택해야 합니다.

운영상, DecryptBlobFile 기술은 각 BLOB에 대한 URL 및 SAS 토큰을 입력으로 사용하고, Azure Cognitive Search에서 기대하는 파일 참조 계약을 사용하여 다운로드한 암호 해독된 파일을 출력합니다. 암호 해독을 수행하려면 DecryptBlobFile에 암호화 키가 필요하다는 점을 기억하십시오. 설정의 일부로 Azure Key Vault의 암호화 키에 대한 DecryptBlobFile 함수 액세스 권한을 부여하는 액세스 정책도 만듭니다.

1. [DecryptBlobFile 방문 페이지](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile#deployment)에 있는 **Azure에 배포** 단추를 클릭하면 Azure Portal 내에서 제공된 Resource Manager 템플릿이 열립니다.

1. **Azure Key Vault 인스턴스가 존재하는 구독** 을 선택하고(다른 구독을 선택하는 경우 이 안내선은 작동하지 않음) 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다(새 리소스 그룹을 만들 경우 배포할 지역을 선택해야 함).

1. **검토 + 만들기** 를 선택하고 사용 약관에 동의했는지 확인한 다음 **만들기** 를 선택하여 Azure 함수를 배포합니다.

    ![포털의 ARM 템플릿](media/indexing-encrypted-blob-files/arm-template.jpg "포털의 ARM 템플릿")

1. 배포가 완료될 때까지 기다립니다.

1. 포털에서 Azure Key Vault 인스턴스로 이동합니다. 사용자 지정 기술에 대한 키 액세스 권한을 부여하는 [액세스 정책](../key-vault/general/assign-access-policy-portal.md) Azure Key Vault에 만듭니다.
 
    1. **설정** 아래에서 **액세스 정책** 을 선택한 다음, **액세스 정책 추가** 를 선택합니다.
     
       ![Keyvault 액세스 정책 추가](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Keyvault 액세스 정책")

    1. **템플릿에서 구성** 아래에서 **Azure Data Lake Storage 또는 Azure Storage** 를 선택합니다.

    1. 보안 주체에 대해 배포한 Azure 함수 인스턴스를 선택합니다. 2단계에서 생성하는 데 사용된 리소스 접두사를 사용하여 검색할 수 있습니다. 여기에는 기본 접두사 값인 **psdbf-function-app** 이 포함됩니다.

    1. 권한 있는 애플리케이션 옵션은 아무것도 선택하지 마십시오.
     
        ![Keyvault 액세스 정책 템플릿 추가](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Keyvault 액세스 정책 템플릿")

    1. 다른 페이지로 이동하여 액세스 정책을 실제로 추가하려면 먼저 액세스 정책 페이지에서 **저장** 을 클릭해야 합니다.
     
         ![Keyvault 액세스 정책 저장](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Keyvault 액세스 정책 저장")

1. 포털에서 **psdbf-function-app** 함수로 이동하고 나중에 이 안내선에서 필요할 때 다음 속성을 기록해 둡니다.

    1. 함수 URL은 함수에 대한 기본 페이지의 **Essentials** 아래에서 찾을 수 있습니다.
    
        ![함수 URL](media/indexing-encrypted-blob-files/function-uri.jpg "Azure 함수 URL을 찾을 수 있는 위치")

    1. 호스트 키 코드는 **앱 키** 로 이동하고 클릭하여 **기본** 키를 표시하고 값을 복사하여 찾을 수 있습니다.
     
        ![함수 호스트 키 코드](media/indexing-encrypted-blob-files/function-host-key.jpg "Azure 함수 호스트 키 코드를 찾을 수 있는 위치")

### <a name="cognitive-services"></a>Cognitive Services

AI 보강 및 기술 세트 실행은 자연어와 이미지 처리를 위한 Text Analytics 및 Computer Vision을 포함한 Cognitive Services를 통해 지원됩니다. 실제 프로토타입 또는 프로젝트를 완료하는 것이 목표라면 이 시점에서 Cognitive Services를 인덱싱 작업에 연결할 수 있도록 Azure Cognitive Search와 동일한 지역에 프로비저닝합니다.

그러나 Azure Cognitive Search는 백그라운드에서 Cognitive Services에 연결하여 인덱서 실행당 20개의 체험 트랜잭션을 제공할 수 있으므로 이 연습에서는 리소스 프로비저닝을 건너뛸 수 있습니다. 20개의 문서를 처리한 후 Cognitive Services 키가 기술 세트에 연결되어 있지 않으면 인덱서가 실패합니다. 대규모 프로젝트의 경우 종량제 S0 계층에서 Cognitive Services를 프로비저닝할 계획입니다. 자세한 내용은 [Cognitive Services 연결](cognitive-search-attach-cognitive-services.md)을 참조하세요. 선택한 인식 기술이 Cognitive Services에 연결되어 있지 않은 경우에도(예: 기술이 추가되지 않은 경우 제공된 기술 세트) Cognitive Services 키는 20개 이상의 문서와 함께 기술 세트를 실행하는 데 필요합니다.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

마지막 구성 요소는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 이 안내선은 무료 계층을 사용하여 완료할 수 있습니다. 

Azure 함수와 마찬가지로 잠시 시간을 내어 관리 키를 수집합니다. 또한 요청을 구조화하기 시작할 때 각 요청을 인증하는 데 사용되는 엔드포인트 및 관리 API 키를 제공해야 합니다.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 검색 서비스의 이름을 확인합니다. 엔드포인트 URL을 검토하여 서비스 이름을 확인할 수 있습니다. 엔드포인트 URL이 `https://mydemo.search.windows.net`인 경우 서비스 이름은 `mydemo`입니다.

2. **설정** > **키** 에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![서비스 이름과 관리자 및 쿼리 키 확인](media/search-get-started-javascript/service-name-and-keys.png)

모든 요청에서 서비스에 보내는 각 요청의 헤더마다 API 키가 필요합니다. 유효한 키는 요청을 보내는 애플리케이션과 이 요청을 처리하는 서비스 간에 요청별로 신뢰를 설정합니다.

## <a name="2---set-up-postman"></a>2 - Postman 설정

Postman을 설치하고 설정합니다.

### <a name="download-and-install-postman"></a>Postman 다운로드 및 설치

1. [Postman 컬렉션 소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)를 다운로드합니다.
1. **파일** > **가져오기** 를 차례로 선택하여 소스 코드를 Postman으로 가져옵니다.
1. **컬렉션** 탭, **...** (줄임표) 단추를 차례로 선택합니다.
1. **편집** 을 선택합니다. 
   
   ![탐색 결과를 표시하는 Postman 앱](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Postman의 [편집] 메뉴로 이동")
1. **편집** 대화 상자에서 **변수** 탭을 선택합니다. 

**변수** 탭에서 이중 중괄호 안에 특정 변수가 있을 때마다 Postman에서 교환하는 값을 추가할 수 있습니다. 예를 들어 Postman에서 `{{admin-key}}` 기호를 `admin-key`에 설정한 현재 값으로 바꿉니다. Postman은 URL, 헤더, 요청 본문 등에서 이 대체를 수행합니다. 

`admin-key`에 대한 값을 얻으려면 앞에서 적어둔 Azure Cognitive Search 관리자 API 키를 사용합니다. 사용 중인 Azure Cognitive Search 서비스의 이름으로 `search-service-name`을 설정합니다. 스토리지 계정의 **액세스 키** 탭에서 값을 사용하여 `storage-connection-string`을 설정하고, 암호화된 파일이 저장되는 해당 스토리지 계정의 BLOB 컨테이너 이름으로 `storage-container-name`을 설정합니다. 이전에 적어둔 Azure 함수 URL로 `function-uri`를 설정하고, 이전에 적어둔 Azure 함수 호스트 키 코드로 `function-code`를 설정합니다. 다른 값에는 기본값을 그대로 둘 수 있습니다.

![Postman 앱 변수 탭](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman의 변수 창")

| 변수    | 가져올 수 있는 위치 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search 서비스의 **키** 페이지.  |
| `search-service-name` | Azure Cognitive Search 서비스의 이름. URL은 `https://{{search-service-name}}.search.windows.net`입니다. |
| `storage-connection-string` | 스토리지 계정의 **액세스 키** 탭에서 **key1** > **연결 문자열** 을 차례로 선택합니다. |
| `storage-container-name` | 인덱싱할 암호화된 파일을 포함하는 BLOB 컨테이너의 이름입니다. |
| `function-uri` |  Azure 함수의 **Essentials** 아래에 있는 기본 페이지. |
| `function-code` | Azure 함수에서 **앱 키** 로 이동한 후 클릭하여 **기본** 키를 표시하고 값을 복사합니다. |
| `api-version` | **2020-06-30** 으로 둡니다. |
| `datasource-name` | **encrypted-blobs-ds** 로 둡니다. |
| `index-name` | **encrypted-blobs-idx** 로 둡니다. |
| `skillset-name` | **encrypted-blobs-ss** 로 둡니다. |
| `indexer-name` | **encrypted-blobs-ixr** 로 둡니다. |

### <a name="review-the-request-collection-in-postman"></a>Postman에서 요청 컬렉션 검토

이 안내선을 실행하는 경우 4개의 HTTP 요청을 실행해야 합니다.

- **인덱스를 만들기 위한 PUT 요청**: 이 인덱스는 Azure Cognitive Search에서 사용하고 반환하는 데이터를 포함합니다.
- **데이터 원본을 만들기 위한 POST 요청**: 이 데이터 원본은 Azure Cognitive Search 서비스를 스토리지 계정에 연결하므로 암호화된 BLOB 파일에 연결합니다. 
- **기술 세트를 만들기 위한 PUT 요청**: 기술 세트는 BLOB 파일 데이터를 암호 해독하는 Azure 함수에 대한 사용자 지정 기술 정의를 지정하고, 문서를 해독한 후 각 문서에서 텍스트를 추출하는 [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)을 지정합니다.
- **인덱서를 만들기 위한 PUT 요청**: 인덱서를 실행하면 데이터를 읽고, 기술 세트를 적용하고, 결과를 저장합니다. 이 요청은 마지막으로 실행해야 합니다.

[소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)에는 네 개의 요청뿐만 아니라 몇 가지 유용한 추가 작업 요청이 있는 Postman 컬렉션이 포함되어 있습니다. 요청을 실행하려면 Postman에서 요청에 대한 탭을 선택하고 각각에 대해 **보내기** 를 선택합니다.

## <a name="3---monitor-indexing"></a>3 - 인덱싱 모니터링

인덱싱 및 보강은 인덱서 만들기 요청을 제출하는 즉시 시작됩니다. 스토리지 계정에 있는 문서 수에 따라 인덱싱하는 데 다소 시간이 걸릴 수 있습니다. 인덱서가 아직 실행 중인지 확인하려면 Postman 컬렉션의 일부로 제공되는 **인덱서 상태 가져오기** 요청을 사용하고 응답을 검토하여 인덱서가 실행 중인지 확인하거나 오류 및 경고 정보를 봅니다.  

무료 계층을 사용할 경우 `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` 메시지가 표시됩니다. 무료 계층의 Blob 인덱싱에는 [문자 추출에 대한 32K 제한](search-limits-quotas-capacity.md#indexer-limits)이 있으므로 이 메시지가 표시됩니다. 더 높은 계층에서는 이 데이터 세트에 대한 이 메시지가 표시되지 않습니다. 

## <a name="4---search"></a>4- 검색

인덱서 실행이 완료되면 일부 쿼리를 실행하여 데이터가 성공적으로 해독되고 인덱싱되는지 확인할 수 있습니다. 포털에서 Azure Cognitive Search 서비스로 이동하고, [검색 탐색기](search-explorer.md)를 사용하여 인덱싱된 데이터에 대해 쿼리를 실행합니다.

## <a name="next-steps"></a>다음 단계

암호화된 파일을 성공적으로 인덱싱했으므로 [인식 기술을 추가하여 이 파이프라인에서 반복](cognitive-search-defining-skillset.md)할 수 있습니다. 이를 통해 데이터에 대한 추가 인사이트를 보강하고 얻을 수 있습니다.

이중으로 암호화된 데이터로 작업하는 경우 Azure Cognitive Search에서 사용할 수 있는 인덱스 암호화 기능을 조사할 수 있습니다. 인덱서는 인덱싱을 위해 해독된 데이터를 요구하지만 인덱스가 있으면 고객 관리형 키를 사용하여 암호화할 수 있습니다. 이렇게 하면 미사용 시 데이터가 항상 암호화됩니다. 자세한 내용은 [Azure Cognitive Search에서 데이터 암호에 대한 고객 관리형 키 구성](search-security-manage-encryption-keys.md)을 참조하세요.