---
title: Azure Data Factory Studio 문제 해결
description: Azure Data Factory Studio 문제를 해결하는 방법을 알아봅니다.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663139"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>Azure Data Factory Studio 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 서비스의 사용자 인터페이스인 [Azure Data Factory Studio의](https://adf.azure.com)일반적인 문제 해결 방법을 살펴봅니다.

## <a name="azure-data-factory-studio-fails-to-load"></a>Azure Data Factory Studio를 로드하지 못함

> [!NOTE]
> Azure Data Factory Studio는 Microsoft Edge 및 Google Chrome을 공식적으로 지원합니다. 다른 웹 브라우저를 사용하면 예기치 않은 동작 또는 문서화되지 않은 동작이 발생할 수 있습니다.

### <a name="third-party-cookies-blocked"></a>타사 쿠키가 차단됨

Azure Data Factory Studio는 브라우저 쿠키를 사용하여 사용자 세션 상태를 유지하며 대화형 개발 및 모니터링 환경을 사용하도록 설정합니다. incognito 세션을 사용하거나 광고 차단을 사용하도록 설정했으므로 브라우저에서 타사 쿠키를 차단할 수 있습니다. 타사 쿠키를 차단하면 포털을 로드할 때 문제가 발생할 수 있습니다.  빈 페이지, ' https://adf.azure.com/accesstoken.html '로 리디렉션되거나 타사 쿠키가 차단되었다는 경고 메시지가 표시될 수 있습니다. 이 문제를 해결하려면 다음 단계를 사용하여 브라우저에서 타사 쿠키 옵션을 사용하도록 설정합니다.

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>모든 쿠키 허용

1. 브라우저에서 **edge://settings/content/cookies** 로 이동합니다.
1. **사이트에서 쿠키 데이터를 저장하고 읽도록 허용** 이 사용하도록 설정되어 있고 **타사 쿠키 차단** 옵션이 사용하지 않도록 설정되어 있는지 확인합니다. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="Edge에서 모든 쿠키를 허용":::
1. Azure Data Factory Studio를 새로 고치고 다시 시도합니다.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Azure Data Factory Studio에서만 쿠키를 사용하도록 허용

모든 쿠키를 허용하지 않으려면 필요에 따라 ADF Studio만 허용하면 됩니다.

1. **edge://settings/content/cookies** 로 이동합니다.
1. **허용** 섹션에서 **추가** 를 선택하고 **adf.azure.com** 사이트를 추가합니다. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="Edge에서 허용 사이트에 ADF UX를 추가":::
1. ADF UX를 새로 고치고 다시 시도하세요.

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>모든 쿠키 허용

1. 브라우저에서 **chrome://settings/cookies** 로 이동합니다.
1. **모든 쿠키 허용** 옵션을 선택합니다. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="Chrome에서 모든 쿠키를 허용":::
1. Azure Data Factory Studio를 새로 고치고 다시 시도합니다.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Azure Data Factory Studio에서만 쿠키를 사용하도록 허용

모든 쿠키를 허용하지 않으려면 필요에 따라 ADF Studio만 허용하면 됩니다.
1. **chrome://settings/cookies** 로 이동합니다.
1. **쿠키를 언제든지 사용할 수 있는 사이트** 옵션에서 **추가** 를 선택합니다. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="Chrome에서 허용된 사이트에 ADF UX를 추가":::
1. **adf.azure.com** 사이트를 추가하고 **모든 쿠키** 옵션을 선택한 다음 저장을 선택합니다. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="ADF UX 사이트의 모든 쿠키 허용":::
1. ADF UX를 새로 고치고 다시 시도하세요.

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Azure Data Factory Studio에서 연결 실패 오류

Azure Data Factory Studio에서 연결 테스트 또는 **미리 보기** 를 클릭한 후 아래 스크린샷과 비슷한 **"연결** 실패" 오류가 표시될 수 있습니다. 즉, 로컬 컴퓨터가 ADF 서비스에 연결할 수 없기 때문에 작업이 실패했습니다.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="연결 실패":::

이 문제를 해결하려면 먼저 브라우저에서 InPrivate 검색 모드를 사용하여 동일한 작업을 시도해 볼 수 있습니다.

그래도 작동하지 않으면 오류 메시지에서 **서버 이름**(이 예제에서는 **dpnortheurope.svc.datafactory.azure.com**)을 찾은 다음, 브라우저의 주소 표시줄에 **서버 이름** 을 직접 입력합니다. 

- 브라우저에서 404가 표시되는 경우 이는 일반적으로 클라이언트 쪽이 양호하고 ADF 서비스 쪽에 문제가 있음을 의미합니다. 오류 메시지의 **활동 ID** 를 사용하여 지원 티켓을 제출합니다.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="리소스를 찾을 수 없음":::

- 404가 표시되지 않거나 브라우저에서 아래와 같은 오류가 표시되면 일반적으로 클라이언트 쪽 문제가 있음을 의미합니다. 추가로 문제 해결 단계를 수행합니다.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="클라이언트 쪽 오류":::

추가 문제를 해결하려면 **명령 프롬프트** 를 열고 `nslookup dpnortheurope.svc.datafactory.azure.com`을 입력합니다. 일반적인 응답은 다음과 같습니다.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="명령 응답 1":::

- 일반적인 DNS(Domain Name Service) 응답이 표시되면 로컬 IT 지원에 문의하여 방화벽 설정을 확인합니다.  이 호스트 이름에 대한 HTTPS 연결이 차단되지 않았는지 확인합니다. 문제가 지속되면 오류 메시지의 **활동 ID를** 제공하는 ADF를 통해 지원 티켓을 제출합니다.

- 위의 일반적인 응답과 다른 DNS 응답은 DNS 이름을 확인할 때 DNS 서버에 문제가 있음을 의미할 수도 있습니다. DNS 서버(예: Google DNS 8.8.8.8)를 변경하면 이 경우 문제를 해결할 수 있습니다. 

- 문제가 지속되면 `nslookup datafactory.azure.com` 및 `nslookup azure.com`을 추가로 시도하여 DNS 확인이 실패한 수준을 확인하고 문제 해결을 위해 모든 정보를 로컬 IT 지원 팀 또는 ISP에게 제출할 수 있습니다. 문제가 여전히 Microsoft 쪽에 있다고 생각되는 경우에는 오류 메시지의 **활동 ID** 를 사용하여 지원 티켓을 제출합니다.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="명령 응답 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>데이터 세트의 연결된 서비스 유형 경고 메시지 변경

활동에서 파일 형식 데이터 세트를 사용하고 나중에 작업에서 이전에 사용한 것과 다른 형식의 연결된 서비스를 가리키려는 경우(예: 파일 시스템에서 Azure Data Lake Storage Gen2로) 경고 메시지가 표시될 수 있습니다.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="경고 메시지":::

파일 형식 데이터 세트는 모든 파일 기반 커넥터와 함께 사용할 수 있습니다. 예를 들어 Azure Blob 또는 Azure Data Lake Storage Gen2에서 Parquet 데이터 세트를 구성할 수 있습니다. 각 커넥터는 서로 다른 앱 모델을 사용하여 작업에 대한 서로 다른 데이터 저장소 관련 설정 집합을 지원합니다. 

ADF 제작 UI에서 복사, 조회, GetMetadata, Delete 작업을 비롯한 활동에서 파일 형식 데이터 세트를 사용하고, 데이터 세트에서 작업의 현재 형식과 다른 형식의 연결된 서비스를 가리키려는 경우(예: 파일 시스템에서 ADLS Gen2 전환) 이 경고 메시지가 표시됩니다. 클린 스위치인지 확인하기 위해 동의하면 이 데이터 세트를 참조하는 파이프라인 및 활동도 새 형식을 사용하도록 수정되고 새 형식과 호환되지 않는 기존 데이터 저장소 설정은 더 이상 적용되지 않아 지워집니다.

각 커넥터에 대해 지원되는 데이터 저장소 설정에 대한 자세한 내용을 보려면 해당 커넥터 문서 -> 복사 작업 속성으로 이동하여 자세한 속성 목록을 볼 수 있습니다. [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) 및 [SFTP](connector-sftp.md)를 참조하세요.


## <a name="could-not-load-resource-while-opening-pipeline"></a>파이프라인을 여는 동안 리소스를 로드할 수 없습니다. 

사용자가 Azure Data Factory Studio를 사용하여 파이프라인에 액세스하면 "'xxxxxx' 리소스를 로드할 수 없습니다.  JSON에서 실수가 없고 참조된 리소스가 있는지 확인합니다. 상태: TypeError: 정의되지 않은 'xxxxx' 속성을 읽을 수 없습니다. 가능한 이유: TypeError: 정의되지 않은 'xxxxxxx' 속성을 읽을 수 없습니다."라는 오류 메시지가 표시됩니다.

오류 메시지의 원본은 파이프라인을 설명하는 JSON 파일입니다. 고객이 Git 통합을 사용하고 파이프라인 JSON 파일이 어떤 이유로 손상된 경우에 발생합니다. 아래와 같이 파이프라인 이름에 오류(x가 있는 빨간색 점)가 표시됩니다.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="파이프라인 JSON 오류":::

솔루션은 처음에 JSON 파일을 수정한 다음 Authoring Tool을 사용하여 파이프라인을 다시 여는 것입니다.


## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

* [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
* [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
* [Azure 비디오](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)