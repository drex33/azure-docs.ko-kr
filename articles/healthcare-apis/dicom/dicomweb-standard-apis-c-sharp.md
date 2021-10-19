---
title: '&trade;C #에서 DICOMweb 표준 api 사용-Azure 의료 api'
description: '이 자습서에서는 c #에서 DICOMweb 표준 Api를 사용 하는 방법을 알아봅니다.'
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: aersoy
ms.openlocfilehash: 2594ff27dd7e4bae5c5463c32a5d4ee3d481cfe8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787649"
---
# <a name="using-dicomwebtrade-standard-apis-with-c"></a>&trade;C에서 DICOMweb 표준 api 사용 #

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 c #을 사용 하 여 DICOM 서비스 작업을 보여 줍니다.

이 자습서에서는 다음 샘플을 사용 합니다 [. DCM DICOM 파일](https://github.com/microsoft/dicom-server/tree/main/docs/dcms).

* blue-circle
* dicom-metadata.csv
* green-square
* red-triangle

샘플 DICOM 파일의 파일 이름, studyUID, seriesUID 및 instanceUID는 다음과 같습니다.

| 파일 | StudyUID | SeriesUID | InstanceUID |
| --- | --- | --- | ---|
|green-square|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.12714725698140337137334606354172323212|
|red-triangle|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652|1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395|
|blue-circle|1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420|1.2.826.0.1.3680043.8.498.77033797676425927098669402985243398207|1.2.826.0.1.3680043.8.498.13273713909719068980354078852867170114|

> [!NOTE]
> 이러한 각 파일은 단일 인스턴스를 나타내며 동일한 연구의 일부입니다. 또한 녹색 사각형 및 빨강 삼각형은 동일한 계열의 일부 이지만 파랑 원은 별도의 계열에 있습니다.

## <a name="prerequisites"></a>필수 조건

DICOMweb 표준 Api를 사용 하려면 &trade; DICOM 서비스의 인스턴스가 배포 되어 있어야 합니다. DICOM 서비스의 인스턴스를 아직 배포 하지 않은 경우 [Azure Portal를 사용 하 여 dicom Service 배포](deploy-dicom-services-in-azure.md)를 참조 하세요.

DICOM 서비스의 인스턴스를 배포한 후에는 App service에 대 한 URL을 검색 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
1. **최근 리소스** 를 검색 하 고 DICOM 서비스 인스턴스를 선택 합니다.
1. DICOM 서비스의 **서비스 URL** 을 복사 합니다. 요청을 만들 때 버전을 url의 일부로 지정 해야 합니다. 자세한 내용은 [DICOM 서비스에 대 한 API 버전 관리 설명서](api-versioning-dicom-service.md)에서 찾을 수 있습니다.

응용 프로그램에서 다음 NuGet 패키지를 설치 합니다.

*  [DICOM 클라이언트](https://microsofthealthoss.visualstudio.com/FhirServer/_packaging?_a=package&feed=Public&package=Microsoft.Health.Dicom.Client&protocolType=NuGet)

*  [dicom](https://www.nuget.org/packages/fo-dicom/)

## <a name="create-a-dicomwebclient"></a>DicomWebClient 만들기

DICOM 서비스를 배포한 후에는 DicomWebClient를 만듭니다. 다음 코드 조각을 실행 하 여이 자습서의 나머지 부분에 사용할 DicomWebClient를 만듭니다. 앞에서 설명한 대로 NuGet 패키지를 모두 설치 했는지 확인 합니다. 토큰을 아직 가져오지 않은 경우 [Azure CLI를 사용 하 여 DICOM 서비스에 대 한 액세스 토큰 가져오기](dicom-get-access-token-azure-cli.md)를 참조 하세요.

```c#
string webServerUrl ="{Your DicomWeb Server URL}"
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri(webServerUrl);
IDicomWebClient client = new DicomWebClient(httpClient);
client.HttpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", “{Your token value}”); 
```
DicomWebClient를 사용 하 여 이제 저장소, 검색, 검색 및 삭제 작업을 수행할 수 있습니다.

## <a name="store-dicom-instances-stow"></a>DICOM 인스턴스 저장 (적재)

이제 만든 DicomWebClient을 사용 하 여 DICOM 파일을 저장할 수 있습니다.

### <a name="store-single-instance"></a>단일 인스턴스 저장

단일 인스턴스 저장 단일 DICOM 파일을 업로드 하는 방법을 보여 줍니다.

_대해_

* 사후 연구

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To blue-circle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile });
```

### <a name="store-instances-for-a-specific-study"></a>특정 학습의 인스턴스를 저장 합니다.

특정 연구의 저장소 인스턴스는 지정 된 연구에 DICOM 파일을 업로드 하는 방법을 보여 줍니다.

_대해_

* /Studies/{study} 게시

```c#
DicomFile dicomFile = await DicomFile.OpenAsync(@"{Path To red-triangle.dcm}");
DicomWebResponse response = await client.StoreAsync(new[] { dicomFile }, "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420");
```

자습서의 다음 부분으로 이동 하기 전에 `green-square.dcm` 위의 방법 중 하나를 사용 하 여 파일을 업로드 합니다.

## <a name="retrieving-dicom-instances-wado"></a>DICOM 인스턴스 검색 (WADO)

다음 코드 조각에서는 이전에 만든 DicomWebClient을 사용 하 여 각 검색 쿼리를 수행 하는 방법을 보여 줍니다.

다음 변수는 나머지 예제 전체에서 사용 됩니다.

```c#
string studyInstanceUid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; //StudyInstanceUID for all 3 examples
string seriesInstanceUid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; //SeriesInstanceUID for green-square and red-triangle
string sopInstanceUid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; //SOPInstanceUID for red-triangle
```

### <a name="retrieve-all-instances-within-a-study"></a>연구 내의 모든 인스턴스를 검색 합니다.

모든 인스턴스 검색은 단일 연구 내의 모든 인스턴스를 검색 합니다.

_대해_

* /Studies/{study} 가져오기

```c#
DicomWebResponse response = await client.RetrieveStudyAsync(studyInstanceUid);
```

이전에 업로드 한 세 개의 dcm 파일은 모두 동일한 연구의 일부 이므로 응답은 3 개의 인스턴스를 모두 반환 해야 합니다. 응답의 상태 코드가 OK이 고 세 인스턴스가 모두 반환 되는지 확인 합니다.

### <a name="use-the-retrieved-instances"></a>검색 된 인스턴스 사용

다음 코드 조각에서는 검색 된 인스턴스에 액세스 하는 방법을 보여 줍니다. 또한 인스턴스의 일부 필드에 액세스 하는 방법과이를 dcm 파일로 저장 하는 방법을 보여 줍니다.

```c#
DicomWebAsyncEnumerableResponse<DicomFile> response = await client.RetrieveStudyAsync(studyInstanceUid);
await foreach (DicomFile file in response)
{
    string patientName = file.Dataset.GetString(DicomTag.PatientName);
    string studyId = file.Dataset.GetString(DicomTag.StudyID);
    string seriesNumber = file.Dataset.GetString(DicomTag.SeriesNumber);
    string instanceNumber = file.Dataset.GetString(DicomTag.InstanceNumber);

    file.Save($"<path_to_save>\\{patientName}{studyId}{seriesNumber}{instanceNumber}.dcm");
}
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>연구에서 모든 인스턴스의 메타 데이터 검색

이 응답은 단일 연구 내의 모든 인스턴스에 대 한 메타 데이터를 검색 합니다.

_대해_

* /Studies/{study}/metadata 가져오기

```c#
DicomWebResponse response = await client.RetrieveStudyMetadataAsync(studyInstanceUid);
```

이전에 업로드 한 세 개의 dcm 파일은 모두 동일한 연구의 일부 이므로 응답은 3 개의 모든 인스턴스에 대 한 메타 데이터를 반환 해야 합니다. 응답의 상태 코드가 OK이 고 모든 메타 데이터가 반환 되는지 확인 합니다.

### <a name="retrieve-all-instances-within-a-series"></a>계열 내의 모든 인스턴스 검색

이 응답은 단일 계열 내의 모든 인스턴스를 검색 합니다.

_대해_

* /Studies/{study}/series/{series} 가져오기


```c#
DicomWebResponse response = await client.RetrieveSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

이 계열에는 두 개의 인스턴스 (녹색 사각형 및 빨간색 삼각형)가 있으므로 응답에서 두 인스턴스를 모두 반환 해야 합니다. 응답의 상태 코드가 OK 인지 확인 하 고 두 인스턴스가 모두 반환 되는지 확인 합니다.

### <a name="retrieve-metadata-of-all-instances-within-a-series"></a>계열 내 모든 인스턴스의 메타 데이터 검색

이 응답은 단일 연구 내의 모든 인스턴스에 대 한 메타 데이터를 검색 합니다.

_대해_

* /Studies/{study}/series/{series}/metadata 가져오기

```c#
DicomWebResponse response = await client.RetrieveSeriesMetadataAsync(studyInstanceUid, seriesInstanceUid);
```

이 계열에는 두 인스턴스 (녹색-사각형 및 빨강 삼각형)가 있으므로 응답에서 두 인스턴스에 대 한 메타 데이터를 반환 해야 합니다. 응답의 상태 코드가 OK 인지 확인 하 고 메타 데이터의 두 인스턴스가 모두 반환 되는지 확인 합니다.

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>일련의 연구에서 단일 인스턴스를 검색 합니다.

이 요청은 단일 인스턴스를 검색 합니다.

_대해_

* /Studies/{study}/series{series}/instances/{instance} 가져오기

```c#
DicomWebResponse response = await client.RetrieveInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

이 응답은 인스턴스 빨간색 삼각형만 반환 해야 합니다. 응답의 상태 코드가 OK이 고 인스턴스가 반환 되는지 확인 합니다.

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>일련의 연구 내에서 단일 인스턴스의 메타 데이터 검색

이 요청은 단일 연구 및 계열 내의 단일 인스턴스에 대 한 메타 데이터를 검색 합니다.

_대해_

* /Studies/{study}/series/{series}/instances/{instance}/metadata 가져오기

```c#
DicomWebResponse response = await client.RetrieveInstanceMetadataAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid);
```

이 응답은 인스턴스 빨간색 삼각형의 메타 데이터만 반환 합니다. 응답의 상태 코드가 OK 인지 확인 하 고 메타 데이터가 반환 되는지 확인 합니다.

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>단일 인스턴스에서 하나 이상의 프레임을 검색 합니다.

이 요청은 단일 인스턴스에서 하나 이상의 프레임을 검색 합니다.

_대해_

* /Studies/{study}/series/{series}/instances/{instance}/frames/{frames} 가져오기

```c#
DicomWebResponse response = await client.RetrieveFramesAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUid, frames: new[] { 1 });

```

이 응답은 빨강 삼각형에서 유일한 프레임을 반환 해야 합니다. 응답의 상태 코드가 양호 인지 확인 하 고 프레임이 반환 되는지 확인 합니다.

## <a name="query-dicom-qido"></a>DICOM 쿼리 (QIDO)

> [!NOTE]
> 지원 되는 DICOM 특성에 대해서는 [Dicom 준수 문을](dicom-services-conformance-statement.md#supported-search-parameters) 참조 하세요.

### <a name="search-for-studies"></a>연구 검색

이 요청은 DICOM 특성에 따라 하나 이상의 연구를 검색 합니다.

_대해_

* /연구를 받으세요. StudyInstanceUID = {연구}

```c#
string query = $"/studies?StudyInstanceUID={studyInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답에 하나의 연구를 포함 하 고 응답 코드가 양호 한지 확인 합니다.

### <a name="search-for-series"></a>계열 검색

이 요청은 DICOM 특성에 따라 하나 이상의 계열을 검색 합니다.

_대해_

* /Series를 가져옵니다. SeriesInstanceUID = {series}

```c#
string query = $"/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답이 하나의 계열을 포함 하 고 응답 코드가 양호 한지 확인 합니다.

### <a name="search-for-series-within-a-study"></a>연구 내에서 시리즈 검색

이 요청은 DICOM 특성에 의해 단일 연구 내에서 하나 이상의 계열을 검색 합니다.

_대해_

* /Studies/{study}/series? SeriesInstanceUID = {series}

```c#
string query = $"/studies/{studyInstanceUid}/series?SeriesInstanceUID={seriesInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답이 하나의 계열을 포함 하 고 응답 코드가 양호 한지 확인 합니다.

### <a name="search-for-instances"></a>인스턴스 검색

이 요청은 DICOM 특성에 따라 하나 이상의 인스턴스를 검색 합니다.

_대해_

* /인스턴스를 가져옵니다. SOPInstanceUID = {instance}

```c#
string query = $"/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

### <a name="search-for-instances-within-a-study"></a>연구 내에서 인스턴스 검색

이 요청은 DICOM 특성에 의해 단일 연구 내에서 하나 이상의 인스턴스를 검색 합니다.

_대해_

* /Studies/{study}/instances? SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

### <a name="search-for-instances-within-a-study-and-series"></a>조사 및 계열 내에서 인스턴스 검색

이 요청은 단일 연구에서 하나 이상의 인스턴스를 검색 하 고 DICOM 특성을 통해 단일 시리즈를 검색 합니다.

_대해_

* /Studies/{study}/series/{series}instances? SOPInstanceUID = {instance}

```c#
string query = $"/studies/{studyInstanceUid}/series/{seriesInstanceUid}/instances?SOPInstanceUID={sopInstanceUid}";
DicomWebResponse response = await client.QueryAsync(query);
```

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

## <a name="delete-dicom"></a>DICOM 삭제

> [!NOTE]
> Delete는 DICOM 표준의 일부가 아니지만 편의를 위해 추가 되었습니다.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>연구 계열 내의 특정 인스턴스 삭제

이 요청은 단일 연구 및 단일 계열에서 단일 인스턴스를 삭제 합니다.

_대해_

* /Studies/{study}/series/{series}/instances/{instance} 삭제

```c#
string sopInstanceUidRed = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395";
DicomWebResponse response = await client.DeleteInstanceAsync(studyInstanceUid, seriesInstanceUid, sopInstanceUidRed);
```

그러면 서버에서 빨간색 삼각형 인스턴스가 삭제 됩니다. 성공 하면 응답 상태 코드에 콘텐츠가 포함 되지 않습니다.

### <a name="delete-a-specific-series-within-a-study"></a>연구 내에서 특정 계열 삭제

이 요청은 단일 연구 내에서 단일 시리즈 (및 모든 자식 인스턴스)를 삭제 합니다.

_대해_

* /Studies/{study}/series/{series} 삭제

```c#
DicomWebResponse response = await client.DeleteSeriesAsync(studyInstanceUid, seriesInstanceUid);
```

이 응답은 서버에서 녹색 사각형 인스턴스 (계열의 왼쪽에 있는 유일한 요소)를 삭제 합니다. 성공 하면 응답 상태 코드에 콘텐츠가 포함 되지 않습니다.

### <a name="delete-a-specific-study"></a>특정 학습 삭제

이 요청은 단일 연구 (및 모든 자식 시리즈 및 인스턴스)를 삭제 합니다.

_대해_

* /Studies/{study} 삭제

```c#
DicomWebResponse response = await client.DeleteStudyAsync(studyInstanceUid);
```

이 응답은 서버에서 파란색 원 인스턴스 (계열의 왼쪽에 있는 유일한 요소)를 삭제 합니다. 성공 하면 응답 상태 코드에 콘텐츠가 포함 되지 않습니다.

### <a name="next-steps"></a>다음 단계

DICOM 서비스에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
