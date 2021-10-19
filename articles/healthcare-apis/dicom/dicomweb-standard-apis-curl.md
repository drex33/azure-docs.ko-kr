---
title: cURL에서 DICOMweb 표준 API 사용 &trade; - Azure Healthcare API
description: 이 자습서에서는 cURL에서 DICOMweb 표준 API를 사용하는 방법을 알아봅니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: afa4b294b71a3cde198001d204d4670cf8ca1ec1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787789"
---
# <a name="using-dicomwebtrade-standard-apis-with-curl"></a>cURL에서 DICOMWeb &trade; Standard API 사용

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 cURL을 사용하여 DICOM 서비스 작업을 보여줍니다.

이 자습서에서는 다음 [샘플 .dcm DICOM 파일](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)를 사용합니다.

* blue-circle.dcm
* dicom-metadata.csv
* green-square.dcm
* red-triangle.dcm 

샘플 DICOM 파일의 파일 이름, studyUID, seriesUID 및 instanceUID는 다음과 같습니다.

| 파일 | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle.dcm|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

>[!NOTE]
>이러한 각 파일은 단일 인스턴스를 나타내며 동일한 연구에 속합니다. 또한 녹색 사각형과 빨간색 삼각형은 동일한 계열의 일부이지만 파란색 원은 별도의 계열에 있습니다.

## <a name="prerequisites"></a>필수 조건

DICOMWeb Standard &trade; API를 사용하려면 DICOM 서비스의 인스턴스가 배포되어 있어야 합니다. DICOM 서비스의 인스턴스를 아직 배포하지 않은 경우 [Azure Portal 사용하여 DICOM 서비스 배포를](deploy-dicom-services-in-azure.md)참조하세요.

DICOM 서비스의 인스턴스를 배포한 후 App Service에 대한 URL을 검색합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
2. **최근 리소스를** 검색하고 DICOM 서비스 인스턴스를 선택합니다.
3. DICOM **서비스의 서비스 URL을** 복사합니다. 
4. 토큰을 아직 가져오지 않은 경우 [Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰 얻기를](dicom-get-access-token-azure-cli.md)참조하세요. 

이 코드의 경우 공개 미리 보기 Azure 서비스에 액세스합니다. PHI(개인 상태 정보)를 업로드하지 않는 것이 중요합니다.


## <a name="working-with-the-dicom-service"></a>DICOM 서비스 작업
 
DICOMweb &trade; Standard는 `multipart/related` DICOM 특정 수락 헤더와 결합된 HTTP 요청을 많이 사용합니다. 다른 REST 기반 API에 익숙한 개발자는 DICOMweb Standard로 작업하는 것이 불편할 수 &trade; 있습니다. 그러나 실행한 후에는 쉽게 사용할 수 있습니다. 시작하는 데는 약간의 숙지만 필요합니다.

cURL 명령은 각각 하나 이상의 변수를 포함하며 경우에 따라 교체해야 하는 변수를 두 개 이상 포함합니다. 명령 실행을 간소화하려면 다음 변수를 검색하고 특정 값으로 바꿔서 바꿉니다.

* {서비스 URL} Azure Portal 프로비전한 DICOM 서비스에 액세스하기 위한 URL입니다(예: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` ). 요청할 때 버전을 URL의 일부로 지정해야 합니다. 자세한 내용은 [DICOM 서비스용 API 버전 버전 설명서](api-versioning-dicom-service.md)에서 찾을 수 있습니다.
* {path-to-dicoms} - red-triangle.dcm 파일이 포함된 디렉터리 경로입니다(예: ). `C:/dicom-server/docs/dcms`
    * 슬래시를 구분 기호로 사용하고 후행 슬래시 _없이_ 디렉터리를 종료해야 합니다.


## <a name="uploading-dicom-instances-stow"></a>DICOM 인스턴스 업로드(STOW)

### <a name="store-instances-using-multipartrelated"></a>Store-instances-using-multipart/related

이 요청은 다중 파트/관련을 사용하여 DICOM 파일을 업로드하는 방법을 보여 드리기 위한 것입니다. 

>[!NOTE]
>DICOM 서비스는 DICOM 표준보다 더 적습니다. 그러나 아래 예제에서는 표준을 엄격하게 준수하는 POST 요청을 보여줍니다.

_세부 정보:_

* 경로:.. /studies
* 메서드: POST
* Headers:
    * 수락: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * 권한 부여: Bearer {token value}
* 본문:
    * Content-Type: 업로드된 각 파일에 대한 application/dicom을 경계 값으로 구분합니다.

일부 프로그래밍 언어와 도구는 다르게 동작합니다. 예를 들어 일부 경우에는 고유한 경계를 정의해야 합니다. 이러한 경우 약간 수정된 Content-Type 헤더를 사용해야 할 수 있습니다. 다음이 성공적으로 사용되었습니다.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\""
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/red-triangle.dcm;type=application/dicom"
--trace-ascii "trace.txt"
```

### <a name="store-instances-for-a-specific-study"></a>특정 연구를 위한 인스턴스 저장

이 요청은 지정된 연구와 관련된 다중 파트/관련 을 사용하여 DICOM 파일을 업로드하는 방법을 보여줍니다.

_세부 정보:_
* 경로:.. /study/{study}
* 메서드: POST
* Headers:
    * 수락: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * 권한 부여: Bearer {token value}
* 본문:
    * Content-Type: 업로드된 각 파일에 대한 application/dicom을 경계 값으로 구분합니다.

일부 프로그래밍 언어와 도구는 다르게 동작합니다. 예를 들어 일부 경우에는 고유한 경계를 정의해야 합니다. 이러한 경우 약간 수정된 Content-Type 헤더를 사용해야 할 수 있습니다. 다음이 성공적으로 사용되었습니다.

 * Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
 * Content-Type: multipart/related; boundary=ABCD1234
 * Content-Type: multipart/related

```
curl --request POST "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Content-Type: multipart/related; type=\"application/dicom\"
--header "Authorization: Bearer {token value}"
--form "file1=@{path-to-dicoms}/blue-circle.dcm;type=application/dicom"
```

### <a name="store-single-instance"></a>Store-single-instance

> [!NOTE]
> 다중 파트/관련 POST를 구성할 필요 없이 단일 DICOM 파일을 업로드할 수 있는 비표준 API입니다. cURL은 다중 파트/관련 작업을 잘 처리하지만 이 API를 사용하면 Postman과 같은 도구가 DICOM 서비스에 파일을 업로드할 수 있습니다.

단일 DICOM 파일을 업로드하려면 다음 메서드가 필요합니다.

_세부 정보:_
* 경로:.. /studies
* 메서드: POST
* Headers:
   * 수락: application/dicom+json
   * Content-Type: application/dicom
   * 권한 부여: Bearer {token value}
* 본문:
    * 단일 DICOM 파일을 이진 바이트로 포함합니다.

```
curl --location --request POST "{Service URL}/v{version}/studies"
--header "Accept: application/dicom+json"
--header "Content-Type: application/dicom"
--header "Authorization: Bearer {token value}"
--data-binary "@{path-to-dicoms}/green-square.dcm"
```

## <a name="retrieving-dicom-wado"></a>DICOM 검색(WADO)

### <a name="retrieve-all-instances-within-a-study"></a>연구 내의 모든 인스턴스 검색

이 요청은 단일 연구 내의 모든 인스턴스를 검색하고 다중 파트/관련 바이트의 컬렉션으로 반환합니다.

_세부 정보:_
* 경로:.. /study/{study}
* 메서드: GET
* Headers:
   * 수락: multipart/related; type="application/dicom"; transfer-syntax=*
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

이 cURL 명령은 출력 파일(suppressWarnings.txt)에 다운로드한 바이트를 표시하지만, 이는 직접 DICOM 파일이 아니며 다중 파트/관련 다운로드의 텍스트 표현입니다.

### <a name="retrieve-metadata-of-all-instances-in-study"></a>연구에 있는 모든 인스턴스의 메타데이터 검색

이 요청은 단일 연구 내의 모든 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/metadata
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer {token value}

이 cURL 명령은 출력 파일(suppressWarnings.txt)에 다운로드한 바이트를 표시하지만, 이는 직접 DICOM 파일이 아니며 다중 파트/관련 다운로드의 텍스트 표현입니다.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-all-instances-within-a-series"></a>계열 내의 모든 인스턴스 검색

이 요청은 단일 계열 내의 모든 인스턴스를 검색하고 다중 파트/관련 바이트의 컬렉션으로 반환합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}
* 메서드: GET
* Headers:
   * 수락: multipart/related; type="application/dicom"; transfer-syntax=*
   * 권한 부여: Bearer {token value}

이 cURL 명령은 다운로드한 바이트를 출력 파일(suppressWarnings.txt)에 표시하지만 DICOM 파일이 아니라 다중 파트/관련 다운로드의 텍스트 표현만 표시합니다.

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: multipart/related; type=\"application/dicom\"; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>계열 내 모든 인스턴스의 메타데이터 검색

이 요청은 단일 연구 내의 모든 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}/metadata
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```
 
### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>일련의 연구 내에서 단일 인스턴스 검색

이 요청은 단일 인스턴스를 검색하고 DICOM 형식의 바이트 스트림으로 반환합니다.

_세부 정보:_
* 경로:.. /study/{study}/series{series}/instances/{instance}
* 메서드: GET
* Headers:
   * 수락: application/dicom; transfer-syntax=*
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom; transfer-syntax=*"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>일련의 연구 내에서 단일 인스턴스의 메타데이터 검색

이 요청은 단일 연구 및 계열 내에서 단일 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}/instances/{instance}/metadata
* 메서드: GET
* Headers:
  * 수락: application/dicom+json
  * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/metadata"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>단일 인스턴스에서 하나 이상의 프레임 검색

이 요청은 단일 인스턴스에서 하나 이상의 프레임을 검색하고 다중 파트/관련 바이트의 컬렉션으로 반환합니다. 쉼표로 구분된 프레임 번호 목록을 전달하여 여러 프레임을 검색할 수 있습니다.  이미지가 있는 모든 DICOM 인스턴스에는 적어도 하나의 프레임이 있으며, 이는 인스턴스 자체와 연결된 이미지일 뿐입니다.

_세부 정보:_
* 경로:.. /study/{study}/series{series}/instances/{instance}/frames/1,2,3
* 메서드: GET
* Headers:
   * 수락: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1(기본값) 또는
   * 수락: multipart/related; type="application/octet-stream"; transfer-syntax=* 또는
   * 수락: multipart/related; type="application/octet-stream";
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395/frames/1"
--header "Accept: multipart/related; type=\"application/octet-stream\"; transfer-syntax=1.2.840.10008.1.2.1"
--header "Authorization: Bearer {token value}"
--output "suppressWarnings.txt"
```

## <a name="query-dicom-qido"></a>DICOM 쿼리(QIDO)

다음 예제에서는 고유 식별자를 사용하여 항목을 검색합니다. 와 같은 다른 특성을 검색할 수도 `PatientName` 있습니다.

### <a name="search-for-studies"></a>연구 검색

이 요청을 통해 DICOM 특성별 하나 이상의 연구를 검색할 수 있습니다.

지원되는 DICOM 특성에 대한 자세한 내용은 [DICOM 준수 문을 참조하세요.](dicom-services-conformance-statement.md)

_세부 정보:_
* 경로:.. /studies? StudyInstanceUID={study}
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies?StudyInstanceUID=1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series"></a>계열 검색

이 요청을 통해 DICOM 특성별로 하나 이상의 계열을 검색할 수 있습니다.

지원되는 DICOM 특성에 대한 자세한 내용은 [DICOM 준수 문을 참조하세요.](dicom-services-conformance-statement.md)

_세부 정보:_
* 경로:.. /series? SeriesInstanceUID={series}
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-series-within-a-study"></a>연구 내에서 계열 검색

이 요청을 통해 DICOM 특성별 단일 연구 내에서 하나 이상의 계열을 검색할 수 있습니다.

지원되는 DICOM 특성에 대한 자세한 내용은 [DICOM 준수 문을 참조하세요.](dicom-services-conformance-statement.md)

_세부 정보:_
* 경로:.. /study/{study}/series? SeriesInstanceUID={series}
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series?SeriesInstanceUID=1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances"></a>인스턴스 검색

이 요청을 통해 DICOM 특성으로 하나 이상의 인스턴스를 검색할 수 있습니다.

지원 되는 DICOM 특성에 대 한 자세한 내용은 [Dicom 준수 문](dicom-services-conformance-statement.md)을 참조 하세요.

_대해_
* 경로:. 인스턴스? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 {token value}

```
curl --request GET "{Service URL}/v{version}/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study"></a>연구 내에서 인스턴스 검색

이 요청은 DICOM 특성에 의해 단일 연구 내에서 하나 이상의 인스턴스를 검색할 수 있도록 합니다.

지원 되는 DICOM 특성에 대 한 자세한 내용은 [Dicom 준수 문](dicom-services-conformance-statement.md)을 참조 하세요.

_대해_
* 경로:. /studies/{study}/instances? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 {token value} 

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```

### <a name="search-for-instances-within-a-study-and-series"></a>조사 및 계열 내에서 인스턴스 검색

이 요청은 단일 연구에서 하나 이상의 인스턴스를 검색 하 고 DICOM 특성을 사용 하 여 단일 시리즈를 검색 합니다.

지원 되는 DICOM 특성에 대 한 자세한 내용은 [Dicom 준수 문](dicom-services-conformance-statement.md) 을 참조 하세요.

_대해_
* 경로:. /studies/{study}/series/{series}/instances? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 {token value}

```
curl --request GET "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances?SOPInstanceUID=1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Accept: application/dicom+json"
--header "Authorization: Bearer {token value}"
```


## <a name="delete-dicom"></a>DICOM 삭제 

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>연구 계열 내의 특정 인스턴스 삭제

이 요청은 단일 연구 및 단일 계열에서 단일 인스턴스를 삭제 합니다.

Delete는 DICOM 표준의 일부가 아니지만 편의를 위해 추가 되었습니다.

_대해_
* 경로:. /studies/{study}/series/{series}/instances/{instance}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 {token value}  

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652/instances/1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-series-within-a-study"></a>연구 내에서 특정 계열 삭제

이 요청은 단일 연구 내에서 단일 시리즈 (및 모든 자식 인스턴스)를 삭제 합니다.

Delete는 DICOM 표준의 일부가 아니지만 편의를 위해 추가 되었습니다.

_대해_
* 경로:. /studies/{study}/series/{series}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 {token value}

```
curl --request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420/series/1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"
--header "Authorization: Bearer {token value}"
```

### <a name="delete-a-specific-study"></a>특정 학습 삭제

이 요청은 단일 연구 (및 모든 자식 시리즈 및 인스턴스)를 삭제 합니다.

Delete는 DICOM 표준의 일부가 아니지만 편의를 위해 추가 되었습니다.

_대해_
* 경로:. /studies/{study}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 {token value}

```
curl--request DELETE "{Service URL}/v{version}/studies/1.2.826.0.1.3680043.8.498
--header "Authorization: Bearer {token value}"
```

### <a name="next-steps"></a>다음 단계

DICOM 서비스에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
