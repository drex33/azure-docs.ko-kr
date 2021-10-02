---
title: REST 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 REST 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: dcf49b00450836aafdb5b9772744914b8e5cf0e2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391100"
---
# <a name="troubleshoot-the-rest-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 REST 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse에서 REST 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-restsinkcallfailed"></a>오류 코드: RestSinkCallFailed

- **메시지**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **원인**: 이 오류는 데이터 팩터리 또는 Synapse 파이프라인이 HTTP 프로토콜을 통해 REST 엔드포인트와 통신하는데 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드 또는 메시지를 확인하고 원격 서버 문제를 해결합니다.

## <a name="error-code-restsourcecallfailed"></a>오류 코드: RestSourceCallFailed

- **메시지**: `The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **원인**: 이 오류는 Azure Data Factory가 HTTP 프로토콜을 통해 REST 엔드포인트와 통신하는데 요청 작업이 실패한 경우 발생합니다.

- **권장 사항**: 오류 메시지에서 HTTP 상태 코드 또는 요청 URL이나 응답 페이로드를 확인하고 원격 서버 문제를 해결합니다.

## <a name="error-code-restsinkunsupportedcompressiontype"></a>오류 코드: RestSinkUNSupportedCompressionType

- **메시지**: `User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **권장 사항**: REST 싱크에 대해 지원되는 압축 유형을 확인합니다.

## <a name="unexpected-network-response-from-the-rest-connector"></a>REST 커넥터로부터 예기치 않은 네트워크 응답 수신됨

- **증상**: 엔드포인트가 가끔씩 REST 커넥터로부터 예기치 않은 응답(400, 401, 403, 500)을 수신합니다.

- **원인**: REST 원본 커넥터는 연결된 서비스/데이터 세트/복사 원본의 URL 및 HTTP 메서드/헤더/본문을 매개 변수로 사용하여 HTTP 요청을 구성합니다. 이 문제는 이러한 매개 변수 중 하나 이상이 잘못 지정되어 발생한 것일 가능성이 큽니다.

- **해결 방법**: 
    - 명령 프롬프트 창에서 ‘curl’을 사용하여 매개 변수가 원인인지 확인합니다(**Accept** 헤더와 **User-Agent** 헤더는 항상 포함되어야 합니다).
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      명령이 전과 같이 예기치 않은 응답을 반환한다면 예상 응답을 반환할 때까지 앞에 나온 매개 변수를 ‘curl’을 사용하여 수정해 봅니다. 

      ‘curl--help’를 사용하면 명령의 고급 사용법을 확인할 수 있습니다.

    - REST 커넥터가 예기치 않은 응답을 반환하는 경우에만 추가 문제 해결을 위해 Microsoft 지원으로 문의하세요.
    
    - ‘curl’은 SSL 인증서 유효성 검사 문제를 재현하는 데 적합하지 않을 수 있습니다. 일부 시나리오에서는 SSL 인증서 유효성 검사 문제없이 ‘curl’ 명령이 성공적으로 실행됩니다. 하지만 동일한 URL이 브라우저에서 실행될 경우, 클라이언트가 서버와 신뢰를 설정할 수 있도록 하기 위한 SSL 인증서가 반환되지 않습니다.

      앞에 나온 원인에 대해서는 **Postman** 및 **Fiddler** 와 같은 도구가 권장됩니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
