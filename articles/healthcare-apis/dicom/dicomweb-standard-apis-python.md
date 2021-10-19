---
title: Python에서 DICOMweb 표준 API 사용 - Azure Healthcare API
description: 이 자습서에서는 Python에서 DICOMweb 표준 API를 사용하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.date: 07/16/2021
ms.author: aersoy
ms.openlocfilehash: c474409b2dfdbca0a921690b1871c7268bde08d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785519"
---
# <a name="using-dicomwebtrade-standard-apis-with-python"></a>Python에서 DICOMWeb &trade; Standard API 사용

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 Python을 사용하여 DICOM 서비스 작업을 보여줍니다.

이 자습서에서는 다음 [샘플 .dcm DICOM 파일을](https://github.com/microsoft/dicom-server/tree/main/docs/dcms)사용합니다.

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

> [!NOTE]
> 이러한 각 파일은 단일 인스턴스를 나타내며 동일한 연구에 속합니다. 또한 녹색-사각형 및 빨간색 삼각형은 동일한 계열의 일부인 반면 파란색 원은 별도의 계열에 있습니다.

## <a name="prerequisites"></a>필수 조건

DICOMWeb 표준 &trade; API를 사용하려면 DICOM 서비스의 인스턴스가 배포되어 있어야 합니다. DICOM 서비스를 아직 배포하지 않은 경우 [Azure Portal 사용하여 DICOM 서비스 배포를](deploy-dicom-services-in-azure.md)참조하세요.

DICOM 서비스의 인스턴스를 배포한 후 App Service의 URL을 검색합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인합니다.
1. **최근 리소스를** 검색하고 DICOM 서비스 인스턴스를 선택합니다.
1. DICOM **서비스의 서비스 URL을** 복사합니다. 
2. 토큰을 아직 가져오지 않은 경우 [Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰 얻기를](dicom-get-access-token-azure-cli.md)참조하세요. 

이 코드의 경우 공개 미리 보기 Azure 서비스에 액세스합니다. PHI(개인 상태 정보)를 업로드하지 않는 것이 중요합니다.

## <a name="working-with-the-dicom-service"></a>DICOM 서비스 작업

DICOMweb &trade; Standard는 `multipart/related` DICOM 특정 수락 헤더와 결합된 HTTP 요청을 많이 사용합니다. 다른 REST 기반 API에 익숙한 개발자는 DICOMweb 표준 작업을 자주 &trade; 찾습니다. 그러나 실행한 후에는 쉽게 사용할 수 있습니다. 시작하는 데 약간의 숙지만 필요합니다.

### <a name="import-the-appropriate-python-libraries"></a>적절한 Python 라이브러리 가져오기

먼저, 필요한 Python 라이브러리를 가져옵니다.

동기 라이브러리를 사용하여 이 예제를 구현하도록 `requests` 선택했습니다. 비동기 지원을 위해 또는 `httpx` 다른 비동기 라이브러리를 사용하는 것이 좋습니다. 또한 요청 작업을 지원하기 위해 에서 두 개의 지원 `urllib3` 함수를 `multipart/related` 가져옵니다.

또한 `DefaultAzureCredential` Azure에 로그인하고 토큰을 가져오기 위해 를 가져옵니다.

```python
import requests
import pydicom
from pathlib import Path
from urllib3.filepost import encode_multipart_formdata, choose_boundary
from azure.identity import DefaultAzureCredential
```

### <a name="configure-user-defined-variables-to-be-used-throughout"></a>전체에서 사용할 사용자 정의 변수 구성

{ }에 래핑된 모든 변수 값을 사용자 고유의 값으로 대체합니다. 또한 구성된 변수가 올바른지 확인합니다.  예를 들어 `base_url` 는 서비스 URL을 사용하여 생성한 다음 사용 중인 REST API 버전과 함께 추가됩니다. DICOM 서비스의 서비스 URL은 ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 입니다. Azure Portal을 사용하여 DICOM 서비스로 이동하여 서비스 URL을 가져올 수 있습니다. 버전에 대한 자세한 내용은 [DICOM 서비스 설명서의 API 버전](api-versioning-dicom-service.md) 을 참조하세요. 사용자 지정 URL을 사용하는 경우 해당 값을 사용자 고유의 값으로 재정의해야 합니다.

```python
dicom_service_name = "{server-name}"
path_to_dicoms_dir = "{path to the folder that includes green-square.dcm and other dcm files}"

base_url = f"{Service URL}/v{version}"

study_uid = "1.2.826.0.1.3680043.8.498.13230779778012324449356534479549187420"; #StudyInstanceUID for all 3 examples
series_uid = "1.2.826.0.1.3680043.8.498.45787841905473114233124723359129632652"; #SeriesInstanceUID for green-square and red-triangle
instance_uid = "1.2.826.0.1.3680043.8.498.47359123102728459884412887463296905395"; #SOPInstanceUID for red-triangle
```

### <a name="authenticate-to-azure-and-get-a-token"></a>Azure에 인증 및 토큰 받기

`DefaultAzureCredential` 을 사용하면 서비스에 로그인할 토큰을 얻는 다양한 방법을 얻을 수 있습니다. 를 사용하여 `AzureCliCredential` 서비스에 로그인할 토큰을 얻습니다. 및 와 같은 다른 자격 증명 `ManagedIdentityCredential` `EnvironmentCredential` 공급자도 사용할 수 있습니다. AzureCliCredential을 사용하려면 이 코드를 실행하기 전에 CLI에서 Azure에 로그인해야 합니다. 자세한 내용은 [Azure CLI 사용하여 DICOM 서비스에 대한 액세스 토큰 얻기를 참조하세요.](dicom-get-access-token-azure-cli.md) 또는 CLI에서 로그인하는 동안 검색된 토큰을 복사하여 붙여넣을 수 있습니다.

> [!NOTE]
> `DefaultAzureCredential` 는 여러 가지 다른 자격 증명 개체를 반환합니다. `AzureCliCredential`를 반환된 컬렉션의 5번째 항목으로 참조합니다. 이는 일관되지 않을 수 있습니다. 그렇다면 줄의 압축을 풀 수 `print(credential.credential)` 있습니다. 그러면 모든 항목이 나열됩니다. Python이 0 기반 인덱싱을 사용한다는 사실을 떠올릴 때 올바른 인덱스 찾기

> [!NOTE]
> CLI를 사용하여 Azure에 로그인하지 않은 경우 실패합니다. 이 작업을 수행하려면 CLI에서 Azure에 로그인해야 합니다. 

```python
from azure.identity import DefaultAzureCredential
credential = DefaultAzureCredential()

#print(credential.credentials) # this can be used to find the index of the AzureCliCredential
token = credential.credentials[4].get_token('https://dicom.healthcareapis.azure.com')
bearer_token = f'Bearer {token.token}'
```

### <a name="create-supporting-methods-to-support-multipartrelated"></a>지원할 지원 메서드 만들기 `multipart\related`

`Requests`라이브러리(및 대부분의 Python 라이브러리)는 `multipart\related` DICOMweb 을 지원하는 방식으로 작동하지 &trade; 않습니다. 이러한 라이브러리로 인해 DICOM 파일 작업을 지원하는 몇 가지 메서드를 추가해야 합니다.

`encode_multipart_related` 에서는 일련의 필드(DICOM의 경우 이러한 라이브러리는 일반적으로 10부 추가로 구성됨) 및 선택적 사용자 정의 경계를 가져옵니다. 사용할 수 있는 content_type 함께 전체 본문을 반환합니다.

```python
def encode_multipart_related(fields, boundary=None):
    if boundary is None:
        boundary = choose_boundary()

    body, _ = encode_multipart_formdata(fields, boundary)
    content_type = str('multipart/related; boundary=%s' % boundary)

    return body, content_type
```

### <a name="create-a-requests-session"></a>세션 만들기 `requests`

`requests`DICOM 서비스와 통신하는 데 사용할 라는 세션을 `client` 만듭니다.


```python
client = requests.session()
```

### <a name="verify-authentication-is-configured-correctly"></a>인증이 올바르게 구성되었는지 확인

인증에 성공하면 200을 반환하는 변경 피드 API 엔드포인트를 호출합니다.

```python
headers = {"Authorization":bearer_token}
url= f'{base_url}/changefeed'

response = client.get(url,headers=headers)
if (response.status_code != 200):
    print('Error! Likely not authenticated!')
```

## <a name="uploading-dicom-instances-stow"></a>DICOM 인스턴스 업로드(STOW)

다음 예제에서는 DICOM 파일 유지를 강조 표시합니다.

### <a name="store-instances-using-multipartrelated"></a>를 사용하여 인스턴스 저장 `multipart/related`

이 예제에서는 단일 DICOM 파일을 업로드하는 방법을 보여 하며, 약간의 Python을 사용하여 DICOM 파일(바이트)을 메모리에 미리 로드합니다. 의 필드 매개 변수에 파일 배열을 전달하면 `encode_multipart_related` 여러 파일을 단일 POST로 업로드할 수 있습니다. 경우에 따라 전체 시리즈 또는 연구 내에 여러 인스턴스를 업로드하는 데 사용됩니다.

_세부 정보:_

* 경로:.. /studies
* 메서드: POST
* Headers:
    * 수락: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * 권한 부여: Bearer $token"

* 본문:
    * Content-Type: 업로드된 각 파일에 대한 application/dicom을 경계 값으로 구분합니다.

일부 프로그래밍 언어와 도구는 다르게 동작합니다. 예를 들어 그 중 일부는 고유한 경계를 정의해야 합니다. 이러한 경우 약간 수정된 Content-Type 헤더를 사용해야 할 수 있습니다. 다음이 성공적으로 사용되었습니다.
* Content-Type: multipart/related; type="application/dicom"; boundary=ABCD1234
* Content-Type: multipart/related; boundary=ABCD1234
* Content-Type: multipart/related

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    rawfile = reader.read()
files = {'file': ('dicomfile', rawfile, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```

### <a name="store-instances-for-a-specific-study"></a>특정 연구를 위한 인스턴스 저장

이 예제에서는 지정된 연구에 여러 DICOM 파일을 업로드하는 방법을 보여줍니다. 비트 Python을 사용하여 DICOM 파일(바이트)을 메모리에 미리 로드합니다.  

의 필드 매개 변수에 파일 배열을 전달하면 `encode_multipart_related` 여러 파일을 단일 POST로 업로드할 수 있습니다. 경우에 따라 전체 시리즈 또는 연구를 업로드하는 데 사용됩니다. 

_세부 정보:_
* 경로:.. /study/{study}
* 메서드: POST
* Headers:
    * 수락: application/dicom+json
    * Content-Type: multipart/related; type="application/dicom"
    * 권한 부여: Bearer $token"
* 본문:
    * Content-Type: 업로드된 각 파일에 대한 application/dicom을 경계 값으로 구분합니다.


```python

filepath_red = Path(path_to_dicoms_dir).joinpath('red-triangle.dcm')
filepath_green = Path(path_to_dicoms_dir).joinpath('green-square.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath_red,'rb') as reader:
    rawfile_red = reader.read()
with open(filepath_green,'rb') as reader:
    rawfile_green = reader.read()  
       
files = {'file_red': ('dicomfile', rawfile_red, 'application/dicom'),
         'file_green': ('dicomfile', rawfile_green, 'application/dicom')}

#encode as multipart_related
body, content_type = encode_multipart_related(fields = files)

headers = {'Accept':'application/dicom+json', "Content-Type":content_type, "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
```
### <a name="store-single-instance-non-standard"></a>단일 인스턴스 저장(비표준)

다음 코드 예제에서는 단일 DICOM 파일을 업로드하는 방법을 보여줍니다. 요청 본문에서 전송된 이진 바이트로 단일 파일 업로드를 간소화하는 비표준 API 엔드포인트입니다.

_세부 정보:_
* 경로:.. /studies
* 메서드: POST
* Headers:
   *  수락: application/dicom+json
   *  Content-Type: application/dicom
   *  권한 부여: Bearer $token"
* 본문:
    * 단일 DICOM 파일을 이진 바이트로 포함합니다.

```python
#upload blue-circle.dcm
filepath = Path(path_to_dicoms_dir).joinpath('blue-circle.dcm')

# Open up and read through file and load bytes into memory 
with open(filepath,'rb') as reader:
    body = reader.read()

headers = {'Accept':'application/dicom+json', 'Content-Type':'application/dicom', "Authorization":bearer_token}

url = f'{base_url}/studies'
response = client.post(url, body, headers=headers, verify=False)
response  # response should be a 409 Conflict if the file was already uploaded in the above request
```

## <a name="retrieve-dicom-instances-wado"></a>DICOM 인스턴스 검색(WADO)

다음 예제에서는 DICOM 인스턴스 검색을 강조 표시합니다.

### <a name="retrieve-all-instances-within-a-study"></a>연구 내의 모든 인스턴스 검색

이 예제에서는 단일 연구 내의 모든 인스턴스를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}
* 메서드: GET
* Headers:
   * 수락: multipart/related; type="application/dicom"; transfer-syntax=*
   * 권한 부여: Bearer $token"

이전에 업로드한 dcm 파일 3개는 모두 동일한 연구에 속하므로 응답에서 세 개의 인스턴스를 모두 반환해야 합니다. 응답에 OK 상태 코드가 있고 세 인스턴스가 모두 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="use-the-retrieved-instances"></a>검색된 인스턴스 사용

인스턴스는 이진 바이트로 검색됩니다. 반환된 항목을 반복하고 바이트를 에서 읽을 수 있는 파일과 같은 파일로 변환할 수 `pydicom` 있습니다.


```python
import requests_toolbelt as tb
from io import BytesIO

mpd = tb.MultipartDecoder.from_response(response)
for part in mpd.parts:
    # Note that the headers are returned as binary!
    print(part.headers[b'content-type'])
    
    # You can convert the binary body (of each part) into a pydicom DataSet
    #   And get direct access to the various underlying fields
    dcm = pydicom.dcmread(BytesIO(part.content))
    print(dcm.PatientName)
    print(dcm.SOPInstanceUID)
```

### <a name="retrieve-metadata-of-all-instances-in-study"></a>연구에 있는 모든 인스턴스의 메타데이터 검색

이 요청은 단일 연구 내의 모든 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/metadata
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer $token"

`.dcm`이전에 업로드한 세 파일은 모두 동일한 연구에 속하므로 응답은 세 인스턴스 모두에 대한 메타데이터를 반환해야 합니다. 응답에 OK 상태 코드가 있고 모든 메타데이터가 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-all-instances-within-a-series"></a>계열 내의 모든 인스턴스 검색

이 요청은 단일 계열 내의 모든 인스턴스를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}
* 메서드: GET
* Headers:
   * 수락: multipart/related; type="application/dicom"; transfer-syntax=*
   * 권한 부여: Bearer $token"

이 계열에는 두 개의 인스턴스(녹색 사각형 및 빨간색 삼각형)가 있으므로 응답은 두 인스턴스를 모두 반환해야 합니다. 응답에 OK 상태 코드가 있고 두 인스턴스가 모두 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
headers = {'Accept':'multipart/related; type="application/dicom"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-all-instances-in-series"></a>계열에 있는 모든 인스턴스의 메타데이터 검색

이 요청은 단일 계열 내의 모든 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}/metadata
* 메서드: GET
* Headers:
   * 수락: application/dicom+json
   * 권한 부여: Bearer $token"

이 계열에는 두 개의 인스턴스(녹색 사각형 및 빨간색 삼각형)가 있으므로 응답은 두 인스턴스에 대해 를 반환해야 합니다. 응답에 OK 상태 코드가 있고 두 인스턴스 메타데이터가 모두 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-a-single-instance-within-a-series-of-a-study"></a>일련의 연구 내에서 단일 인스턴스 검색

이 요청은 단일 인스턴스를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series{series}/instances/{instance}
* 메서드: GET
* Headers:
   * 수락: application/dicom; transfer-syntax=*
   * 권한 부여: Bearer $token"

이 코드 예제에서는 인스턴스 빨간색 삼각형만 반환해야 합니다. 응답에 OK 상태 코드가 있고 인스턴스가 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
headers = {'Accept':'application/dicom; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-metadata-of-a-single-instance-within-a-series-of-a-study"></a>일련의 연구 내에서 단일 인스턴스의 메타데이터 검색

이 요청은 단일 연구 및 계열 내에서 단일 인스턴스에 대한 메타데이터를 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series/{series}/instances/{instance}/metadata
* 메서드: GET
* Headers:
  * 수락: application/dicom+json
  * 권한 부여: Bearer $token"

이 코드 예제에서는 인스턴스 빨간색 삼각형에 대한 메타데이터만 반환해야 합니다. 응답에 OK 상태 코드가 있고 메타데이터가 반환되어 있는지 확인합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/metadata'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

### <a name="retrieve-one-or-more-frames-from-a-single-instance"></a>단일 인스턴스에서 하나 이상의 프레임 검색

이 요청은 단일 인스턴스에서 하나 이상의 프레임을 검색합니다.

_세부 정보:_
* 경로:.. /study/{study}/series{series}/instances/{instance}/frames/1,2,3
* 메서드: GET
* Headers:
   * 권한 부여: 전달자 $token "
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=1.2.840.10008.1.2.1` (기본값) 또는
   * `Accept: multipart/related; type="application/octet-stream"; transfer-syntax=*` 또는
   * `Accept: multipart/related; type="application/octet-stream";`

이 코드 예제는 빨강 삼각형에서 유일한 프레임을 반환 해야 합니다. 응답의 상태 코드가 양호 인지 확인 하 고 프레임이 반환 되는지 확인 합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}/frames/1'
headers = {'Accept':'multipart/related; type="application/octet-stream"; transfer-syntax=*', "Authorization":bearer_token}

response = client.get(url, headers=headers) #, verify=False)
```

## <a name="query-dicom-qido"></a>DICOM 쿼리 (QIDO)

다음 예제에서는 고유 식별자를 사용 하 여 항목을 검색 합니다. PatientName와 같은 다른 특성도 검색할 수 있습니다.

지원 되는 DICOM 특성은 [Dicom 규칙 명세서](dicom-services-conformance-statement.md#supported-search-parameters) 문서를 참조 하세요.

### <a name="search-for-studies"></a>연구 검색

이 요청은 DICOM 특성에 따라 하나 이상의 연구를 검색 합니다.

_대해_
* 경로:. 유용성? StudyInstanceUID = {연구}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답에 하나의 연구를 포함 하 고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/studies'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'StudyInstanceUID':study_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series"></a>계열 검색

이 요청은 DICOM 특성에 따라 하나 이상의 계열을 검색 합니다.

_대해_
* 경로:. 연속? SeriesInstanceUID = {series}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답이 하나의 계열을 포함 하 고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-series-within-a-study"></a>연구 내에서 시리즈 검색

이 요청은 DICOM 특성에 의해 단일 연구 내에서 하나 이상의 계열을 검색 합니다.

_대해_
* 경로:. /studies/{study}/series? SeriesInstanceUID = {series}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답이 하나의 계열을 포함 하 고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/studies/{study_uid}/series'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SeriesInstanceUID':series_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances"></a>인스턴스 검색

이 요청은 DICOM 특성에 따라 하나 이상의 인스턴스를 검색 합니다.

_대해_
* 경로:. 인스턴스? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study"></a>연구 내에서 인스턴스 검색

이 요청은 DICOM 특성에 의해 단일 연구 내에서 하나 이상의 인스턴스를 검색 합니다.

_대해_
* 경로:. /studies/{study}/instances? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/studies/{study_uid}/instances'
headers = {'Accept':'application/dicom+json', "Authorization":bearer_token}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

### <a name="search-for-instances-within-a-study-and-series"></a>조사 및 계열 내에서 인스턴스 검색

이 요청은 단일 연구에서 하나 이상의 인스턴스를 검색 하 고 DICOM 특성을 통해 단일 시리즈를 검색 합니다.

_대해_
* 경로:. /studies/{study}/series/{series}/instances? SOPInstanceUID = {instance}
* 메서드: GET
* Headers:
   * 수락: 응용 프로그램/dicom + json
   * 권한 부여: 전달자 $token "

응답에 하나의 인스턴스가 포함 되어 있고 응답 코드가 양호 한지 확인 합니다.

```python
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances'
headers = {'Accept':'application/dicom+json'}
params = {'SOPInstanceUID':instance_uid}

response = client.get(url, headers=headers, params=params) #, verify=False)
```

## <a name="delete-dicom"></a>DICOM 삭제

> [!NOTE]
> Delete는 DICOM 표준의 일부가 아니지만 편의를 위해 추가 되었습니다.

성공적으로 삭제 되 면 204 응답 코드가 반환 됩니다. 항목이 존재 하지 않거나 이미 삭제 된 경우 404 응답 코드가 반환 됩니다.

### <a name="delete-a-specific-instance-within-a-study-and-series"></a>연구 계열 내의 특정 인스턴스 삭제

이 요청은 단일 연구 및 단일 계열에서 단일 인스턴스를 삭제 합니다.

_대해_
* 경로:. /studies/{study}/series/{series}/instances/{instance}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 $token

이 요청은 서버에서 빨간색 삼각형 인스턴스를 삭제 합니다. 성공 하면 응답 상태 코드에 콘텐츠가 포함 되지 않습니다.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}/instances/{instance_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-series-within-a-study"></a>연구 내에서 특정 계열 삭제

이 요청은 단일 연구 내에서 단일 시리즈 (및 모든 자식 인스턴스)를 삭제 합니다.

_대해_
* 경로:. /studies/{study}/series/{series}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 $token

이 코드 예제에서는 서버에서 녹색 사각형 인스턴스 (계열의 왼쪽에 있는 유일한 요소)를 삭제 합니다. 성공 하는 경우 응답 상태 코드는 콘텐츠가 아닙니다.

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}/series/{series_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="delete-a-specific-study"></a>특정 학습 삭제

이 요청은 단일 연구 (및 모든 자식 시리즈 및 인스턴스)를 삭제 합니다.

_대해_
* 경로:. /studies/{study}
* 메서드: 삭제
* Headers:
   * 권한 부여: 전달자 $token

```python
headers = {"Authorization":bearer_token}
url = f'{base_url}/studies/{study_uid}'
response = client.delete(url, headers=headers) 
```

### <a name="next-steps"></a>다음 단계

DICOM 서비스에 대 한 자세한 내용은 다음을 참조 하세요. 

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)
