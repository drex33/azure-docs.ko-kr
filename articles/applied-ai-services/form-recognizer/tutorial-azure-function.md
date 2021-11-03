---
title: '자습서: Azure 함수를 사용하여 저장된 문서 처리'
titleSuffix: Azure Applied AI Services
description: 이 가이드에서는 Azure 함수를 사용하여 Azure Blob 스토리지 컨테이너에 업로드된 문서를 처리하도록 트리거하는 방법을 보여 줍니다.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: ae69ae943de5d5e83b08a0d654bd102c4bf95bcf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026584"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>자습서: Azure 함수를 사용하여 저장된 문서 처리

Azure Functions를 사용하여 빌드된 자동화된 데이터 처리 파이프라인의 일부로 Form Recognizer를 사용할 수 있습니다. 이 가이드에서는 Azure 함수를 사용하여 Azure Blob 스토리지 컨테이너에 업로드된 문서를 처리하는 방법을 보여 줍니다. 이 워크플로는 Form Recognizer 레이아웃 서비스를 사용하여 저장된 문서에서 테이블 데이터를 추출하고 테이블 데이터를 Azure의 .csv 파일에 저장합니다. 그런 다음, Microsoft Power BI를 사용하여 데이터를 표시할 수 있습니다(여기서는 설명하지 않음).

> [!div class="mx-imgBorder"]
> ![Azure 서비스 워크플로 다이어그램](./media/tutorial-azure-function/workflow-diagram.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Storage 계정 만들기
> * Azure Functions 프로젝트 만들기
> * 업로드된 양식에서 레이아웃 데이터 추출
> * Azure Storage에 레이아웃 데이터 업로드

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Form Recognizer 리소스를 만들어"  target="_blank">Form Recognizer 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a> Form Recognizer 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.
  * 애플리케이션을 Form Recognizer API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 분석할 로컬 PDF 문서. 이 [샘플 문서](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf)를 다운로드하여 사용할 수 있습니다.
* [Python 3.8. x](https://www.python.org/downloads/) 설치
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 설치
* [Azure Functions Core Tools](../../azure-functions/functions-run-local.md?tabs=windows%2ccsharp%2cbash#install-the-azure-functions-core-tools) 설치
* 다음 확장이 설치된 Visual Studio Code
  * [Azure Functions 확장](/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Python 확장](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

Azure Portal에서 [Azure Storage 계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). 계정 종류로 **StorageV2** 를 선택합니다.

왼쪽 창에서 **CORS** 탭을 선택하고, 기존 CORS 정책을 제거합니다(있는 경우).

배포되면 **test** 및 **output** 이라는 두 개의 빈 Blob 스토리지 컨테이너를 만듭니다.

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

Visual Studio Code를 엽니다. Azure Functions 확장이 설치되면 왼쪽 탐색 창에 Azure 로고가 표시됩니다. 이 폴더를 선택합니다. 새 프로젝트를 만들고, 메시지가 표시되면 프로젝트를 포함할 **coa_new** 로컬 폴더를 만듭니다.

![VSCode 함수 만들기 단추](./media/tutorial-azure-function/vs-code-create-function.png)


다음과 같은 여러 설정을 구성하라는 메시지가 표시됩니다.
* **언어 선택** 프롬프트에서 Python을 선택합니다.
* **템플릿 선택** 프롬프트에서 Azure Blob Storage 트리거를 선택합니다. 그런 다음, 기본 트리거에 이름을 지정합니다.
* **설정 선택** 프롬프트에서 새 로컬 설정 앱 만들기를 선택합니다.
* 만든 스토리지 계정이 있는 Azure 구독을 선택합니다. 그런 다음, 스토리지 컨테이너의 이름을 입력해야 합니다(이 경우 `test/{name}`).
* 현재 창에서 프로젝트 열기를 선택합니다. 

![VSCode 만들기 프롬프트 예제](./media/tutorial-azure-function/vs-code-prompt.png)

이러한 단계가 완료되면 VSCode에서 *\_\_init\_\_.py* Python 스크립트를 사용하여 새 Azure 함수 프로젝트를 추가합니다. 이 스크립트는 파일을 **test** 스토리지 컨테이너에 업로드할 때 트리거되지만 아무 작업도 수행하지 않습니다.

## <a name="test-the-function"></a>함수 테스트

F5 키를 눌러 기본 함수를 실행합니다. VSCode에서 인터페이스에 사용할 스토리지 계정을 선택하라는 메시지를 표시합니다. 만든 스토리지 계정을 선택하고 계속합니다.

Azure Storage Explorer를 열고, 샘플 PDF 문서를 **Test** 컨테이너에 업로드합니다. 그런 다음, VSCode 터미널을 확인합니다. 스크립트에서 PDF 업로드를 통해 트리거되었다고 기록해야 합니다.

![VSCode 터미널 테스트](./media/tutorial-azure-function/vs-code-terminal-test.png)


계속하기 전에 스크립트를 중지합니다.

## <a name="add-form-processing-code"></a>양식 처리 코드 추가

다음으로, Form Recognizer 서비스를 호출하고 Form Recognizer [레이아웃 API](concept-layout.md)를 사용하여 업로드된 문서를 구문 분석하는 사용자 고유의 코드를 Python 스크립트에 추가합니다.

VSCode에서 함수의 *requirements.txt* 파일로 이동합니다. 이렇게 하면 스크립트에 대한 종속성이 정의됩니다. 다음 Python 패키지를 파일에 추가합니다.

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

그런 다음, *\_\_init\_\_.py* 스크립트를 엽니다. 다음 `import` 문을 추가합니다.

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

생성된 `main` 함수를 있는 그대로 둘 수 있습니다. 사용자 지정 코드를 이 함수 내에 추가합니다.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

다음 코드 블록에서는 업로드된 문서에서 Form Recognizer [레이아웃 분석](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync) API를 호출합니다. 엔드포인트 및 키 값을 입력합니다. 

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Form Recognizer 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 보안 메서드를 사용하여 자격 증명을 저장하고 액세스합니다. 자세한 내용은 [Cognitive Services 보안](../../cognitive-services/cognitive-services-security.md) 문서를 참조하세요.

다음으로, 서비스를 쿼리하고 반환된 데이터를 가져오는 코드를 추가합니다. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

그런 다음, Azure Storage **output** 컨테이너에 연결하는 다음 코드를 추가합니다. 스토리지 계정 이름 및 키에 대한 사용자 고유의 값을 입력합니다. Azure Portal에 있는 스토리지 리소스의 **액세스 키** 탭에서 키를 가져올 수 있습니다.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

다음 코드에서는 반환된 Form Recognizer 응답을 구문 분석하고, .csv 파일을 생성하고, 이를 **output** 컨테이너에 업로드합니다. 


> [!IMPORTANT]
> 사용자 고유의 양식 문서 구조와 일치하도록 이 코드를 편집해야 할 수 있습니다.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

마지막으로, 코드의 마지막 블록에서는 추출된 테이블과 텍스트 데이터를 Blob 스토리지 요소에 업로드합니다.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>함수 실행

F5 키를 눌러 함수를 다시 실행합니다. Azure Storage Explorer를 사용하여 샘플 PDF 양식을 **Test** 스토리지 컨테이너에 업로드합니다. 이 작업은 실행할 스크립트를 트리거한 다음, **output** 컨테이너에 결과 .csv 파일(테이블로 표시됨)을 표시합니다.

이 컨테이너를 Power BI에 연결하여 포함된 데이터에 대한 풍부한 시각화를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Python으로 작성된 Azure 함수를 사용하여 업로드된 PDF 문서를 자동으로 처리하고 해당 콘텐츠를 보다 더 데이터 친화적인 형식으로 출력하는 방법을 알아보았습니다. 다음으로 Power BI를 사용하여 데이터를 표시하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Form Recognizer란?](overview.md)
* [레이아웃 API](concept-layout.md)에 대해 자세히 알아보기