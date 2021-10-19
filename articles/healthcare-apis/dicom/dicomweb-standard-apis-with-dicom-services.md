---
title: Azure 의료 Api DICOM service에서 DICOMweb 표준 Api 사용
description: 이 자습서에서는 DICOM 서비스에서 DICOMweb 표준 Api를 사용 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/23/2021
ms.author: aersoy
ms.openlocfilehash: b219c4d624c5823804371bba156911aaa94eeccb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631990"
---
# <a name="using-dicomwebtradestandard-apis-with-dicom-services"></a>&trade;DICOM 서비스에서 DICOMweb 표준 api 사용

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 DICOM 서비스에서 DICOMweb 표준 Api를 사용 하는 방법에 대 한 개요를 제공 &trade; 합니다.

DICOM 서비스는 &trade; 다음을 포함 하는 DICOMweb 표준의 하위 집합을 지원 합니다.

* 매장 (적재-RS)
* 검색 (WADO-RS)
* 검색 (QIDO-RS)

또한 다음과 같은 비표준 API가 지원 됩니다.

* 삭제
* 피드 변경

DICOM 웹 표준 Api의 지원에 대 한 자세한 내용은 [Dicom 규격 문](dicom-services-conformance-statement.md) 참조 문서를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

DICOMweb 표준 Api를 사용 하려면 &trade; DICOM 서비스의 인스턴스가 배포 되어 있어야 합니다. DICOM 서비스의 인스턴스를 아직 배포 하지 않은 경우 [Azure Portal를 사용 하 여 dicom Service 배포](deploy-dicom-services-in-azure.md)를 참조 하세요.

배포가 완료 되 면 Azure Portal를 사용 하 여 새로 만든 DICOM 서비스로 이동 하 여 서비스 URL을 비롯 한 세부 정보를 볼 수 있습니다. DICOM 서비스에 액세스 하기 위한 서비스 URL은입니다. ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 요청을 만들 때 버전을 url의 일부로 지정 해야 합니다. 자세한 내용은 [DICOM 서비스에 대 한 API 버전 관리 설명서](api-versioning-dicom-service.md)에서 찾을 수 있습니다.

## <a name="overview-of-various-methods-to-use-with-dicom-service"></a>DICOM 서비스에서 사용 하는 다양 한 방법의 개요

DICOM 서비스는 REST API으로 노출 되므로 모든 최신 개발 언어를 사용 하 여 액세스할 수 있습니다. 서비스 작업에 대 한 언어와 무관 한 정보는 [DICOM 준수 문](dicom-services-conformance-statement.md)을 참조 하세요.

언어별 예제를 보려면 아래 예제를 참조 하세요. Postman collection 예제는 다음을 비롯 한 여러 언어로 볼 수 있습니다.

* Go 
* Java 
* JavaScript 
* C# 
* PHP 
* C 
* NodeJS
* Objective-C
* OCaml
* PowerShell
* Python
* Ruby 
* Swift.

### <a name="c"></a>C#

DICOM 서비스에서 c #을 사용 하는 방법에 대 한 자세한 내용은 [Using DICOMweb™ Standard api with c #](dicomweb-standard-apis-c-sharp.md) 자습서를 참조 하세요.

### <a name="curl"></a>cURL

말아는 거의 모든 운영 체제에 사용할 수 있는 웹 끝점을 호출 하는 일반적인 명령줄 도구입니다. 자습서를 [다운로드](https://curl.haxx.se/download.html) 하 여 시작 하세요.

DICOM 서비스에서 말아를 사용 하는 방법에 대 한 자세한 내용은 [™ DICOMWeb를 사용 하 여 표준 api](dicomweb-standard-apis-curl.md) 를 사용 하는 방법 자습서를 참조 하세요.

### <a name="python"></a>Python

DICOM 서비스에서 Python을 사용 하는 방법에 대 한 자세한 내용은 [DICOMWeb™ Standard api를 사용](dicomweb-standard-apis-python.md) 하 여 python 자습서 (영문)를 참조 하세요.

### <a name="postman"></a>postman

Postman은 REST Api를 디자인, 빌드 및 테스트 하는 데 유용한 도구입니다. [Postman을 다운로드](https://www.postman.com/downloads/) 하 여 시작 하세요. Postman [learning 사이트](https://learning.postman.com/)에서 postman을 효과적으로 사용 하는 방법을 배울 수 있습니다.

Postman과 DICOMweb standard의 중요 한 한 가지 주의할 점은 &trade; Postman이 dicom 표준에 정의 된 단일 파트 페이로드를 사용 하 여 dicom 파일 업로드만 지원할 수 있다는 것입니다. 이러한 이유는 Postman이 다중 파트/관련 POST 요청에서 사용자 지정 구분 기호를 지원할 수 없기 때문입니다. 자세한 내용은 [me # 576에 대해 작동 하지 않는 다중 파트 게시물](https://github.com/postmanlabs/postman-app-support/issues/576)을 참조 하세요. 따라서 다중 파트 요청을 사용 하 여 DICOM 문서를 업로드 하는 Postman 컬렉션의 모든 예제에는 [작동 안 함-설명 참조] 접두사가 붙습니다. 단일 파트 요청을 사용 하 여 업로드 하는 예제는 컬렉션에 포함 되 고 "매장-단일 인스턴스" 접두사가 붙습니다.

Postman collection을 사용 하려면 컬렉션을 로컬로 다운로드 하 고 Postman을 통해 컬렉션을 가져와야 합니다. 이 컬렉션에 액세스 하려면 [Postman Collection 예](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)를 참조 하세요.

## <a name="summary"></a>요약

이 자습서에서는 DICOM 서비스에서 지 원하는 Api에 대 한 개요를 제공 했습니다. 다음 도구를 사용 하 여 이러한 Api 사용을 시작 하세요.

- [C에서 DICOMweb™ 표준 Api 사용 #](dicomweb-standard-apis-c-sharp.md)
- [DICOMWeb를 사용 하 여 표준 Api™ 사용](dicomweb-standard-apis-curl.md)
- [Python에서 DICOMWeb™ Standard Api 사용](dicomweb-standard-apis-python.md)
- [Postman 예제 컬렉션과 함께 DICOM 웹 표준 Api 사용](https://github.com/microsoft/dicom-server/blob/main/docs/resources/Conformance-as-Postman.postman_collection.json)

### <a name="next-steps"></a>다음 단계

자세한 내용은

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
