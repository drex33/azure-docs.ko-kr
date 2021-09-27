---
title: 웹 서비스로 배포된 모델에 대한 클라이언트 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 모델을 배포할 때 생성된 웹 서비스 엔드포인트를 호출하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/12/2020
ms.topic: how-to
ms.custom: devx-track-python,devx-track-csharp
ms.openlocfilehash: 7c95844bf9a639a89fdd39b702c7d6327a9f6fe8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740742"
---
# <a name="consume-an-azure-machine-learning-model-deployed-as-a-web-service"></a>웹 서비스로 배포된 Azure Machine Learning 모델 사용


Azure Machine Learning 모델을 웹 서비스로 배포하면 REST API 엔드포인트가 생성됩니다. 이 엔드포인트로 데이터를 보내고 모델에서 반환된 예측을 받을 수 있습니다. 이 문서에서는 C#, Go, Java 및 Python을 사용하여 웹 서비스용 클라이언트를 만드는 방법에 대해 알아봅니다.

Azure Container 인스턴스, Azure Kubernetes Service 또는 필드 프로그래밍 가능 게이트 배열(FPGA)에 모델을 로컬 환경에 배포할 때 웹 서비스를 만듭니다. [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하여 웹 서비스에 액세스하는 데 사용되는 URI를 검색합니다. 인증이 사용 가능한 경우 SDK를 사용하여 인증 키 또는 토큰을 가져올 수도 있습니다.

기계 학습 웹 서비스를 사용하는 클라이언트를 만들기 위한 일반적인 워크플로는 다음과 같습니다.

1. SDK를 사용하여 연결 정보를 가져옵니다.
1. 모델에서 사용하는 요청 데이터의 형식을 결정합니다.
1. 웹 서비스를 호출하는 애플리케이션을 만듭니다.

> [!TIP]
> 이 문서의 예제는 OpenAPI(Swagger) 사양을 사용하지 않고 수동으로 생성됩니다. 배포에 대한 OpenAPI 사양을 활성화한 경우 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)와 같은 도구를 사용하여 서비스에 대한 클라이언트 라이브러리를 만들 수 있습니다.

## <a name="connection-information"></a>연결 정보

> [!NOTE]
> Azure Machine Learning SDK를 사용하여 웹 서비스 정보를 가져옵니다. 이는 Python SDK입니다. 모든 언어를 사용하여 서비스용 클라이언트를 만들 수 있습니다.

[azureml.core.Webservice](/python/api/azureml-core/azureml.core.webservice%28class%29) 클래스는 클라이언트를 만드는 데 필요한 정보를 제공합니다. 다음 `Webservice` 속성은 클라이언트 애플리케이션을 만드는 데 유용합니다.

* `auth_enabled` - 키 인증을 사용하도록 설정한 경우 `True`이고, 그렇지 않으면 `False`입니다.
* `token_auth_enabled` - 토큰 인증을 사용하도록 설정한 경우 `True`이고, 그렇지 않으면 `False`입니다.
* `scoring_uri` - REST API 주소입니다.
* `swagger_uri` -OpenAPI 사양의 주소입니다. 자동 스키마 생성을 사용하도록 설정한 경우이 URI를 사용할 수 있습니다. 자세한 내용은 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)를 참조하세요.

배포된 웹 서비스에 대해 이 정보를 검색하는 여러 방법이 있습니다.

# <a name="python"></a>[Python](#tab/python)

* 모델을 배포하면 `Webservice` 개체가 서비스에 대한 정보와 함께 반환됩니다.

    ```python
    service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
    service.wait_for_deployment(show_output = True)
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

* `Webservice.list`를 사용하여 작업 영역의 모델에 대해 배포된 웹 서비스 목록을 검색할 수 있습니다. 필터를 추가하여 반환되는 정보의 목록을 좁힐 수 있습니다. 필터링할 수 있는 항목에 대한 자세한 내용은 [Webservice.list](/python/api/azureml-core/azureml.core.webservice.webservice.webservice) 참조 설명서를 참조하세요.

    ```python
    services = Webservice.list(ws)
    print(services[0].scoring_uri)
    print(services[0].swagger_uri)
    ```

* 배포된 서비스의 이름을 알고 있으면 `Webservice`의 새 인스턴스 만들고 작업 영역 및 서비스 이름을 매개 변수로 제공할 수 있습니다. 새 개체에는 배포된 서비스에 대한 정보가 포함되어 있습니다.

    ```python
    service = Webservice(workspace=ws, name='myservice')
    print(service.scoring_uri)
    print(service.swagger_uri)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

배포된 서비스의 이름을 알고 있는 경우 [az ml service show](/cli/azure/ml(v1)/service#az_ml_service_show) 명령을 사용합니다.

```azurecli
az ml service show -n <service-name>
```

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning 스튜디오에서 __엔드포인트__, __실시간 엔드포인트__, 엔드포인트 이름을 차례로 선택합니다. 엔드포인트에 대한 세부 정보에서 __REST 엔드포인트__ 필드는 채점 URI를 포함합니다. __Swagger URI__ 에는 swagger URI가 포함됩니다.

---

다음 표는 이러한 URI는 어떤 것인지 보여줍니다.

| URI 형식 | 예제 |
| ----- | ----- |
| 채점 URI | `http://104.214.29.152:80/api/v1/service/<service-name>/score` |
| Swagger URI | `http://104.214.29.152/api/v1/service/<service-name>/swagger.json` |

> [!TIP]
> IP 주소는 배포에 따라 달라집니다. 각 AKS 클러스터에는 해당 클러스터에 대한 배포에서 공유하는 자체 IP 주소가 있습니다.

### <a name="secured-web-service"></a>보안 웹 서비스

TLS/SSL 인증서를 사용하여 배포된 웹 서비스를 보호한 경우에는 [HTTPS](https://en.wikipedia.org/wiki/HTTPS)를 사용하여 채점 또는 swagger URI를 사용하여 서비스에 연결할 수 있습니다. HTTPS는 둘 간의 통신을 암호화하여 클라이언트와 웹 서비스 간의 통신을 보안하는데 도움이 됩니다. 암호화는 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용합니다. TLS는 TLS의 선행 작업인 *SSL(Secure Sockets Layer)* (SSL)이라고도 합니다.

> [!IMPORTANT]
> Azure Machine Learning에서 배포하는 웹 서비스는 TLS 버전 1.2만 지원합니다. 클라이언트 애플리케이션을 만들 때 이 버전이 지원되는지 확인합니다.

자세한 내용은 [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)를 참조하세요.

### <a name="authentication-for-services"></a>서비스에 대한 인증

Azure Machine Learning은 웹 서비스에 대한 액세스를 제어하는 두 가지 방법을 제공합니다.

|인증 방법|ACI|AKS|
|---|---|---|
|키|기본적으로 사용 안 함| 기본적으로 사용|
|토큰| 사용할 수 없음| 기본적으로 사용 안 함 |

키 또는 토큰으로 보안이 설정된 서비스로 요청을 보낼 때 __권한 부여__ 헤더를 사용하여 키 또는 토큰을 전달합니다. 키 또는 토큰은 `Bearer <key-or-token>`로 형식이 지정되어야 합니다. 여기서 `<key-or-token>`는 키 또는 토큰 값입니다.

키와 토큰의 주요 차이점은 **키가 정적이며 수동으로 다시 생성할 수 있고** **만료 시 토큰은 새로 고쳐야** 하는 것입니다. 키 기반 인증은 Azure Container Instance 및 Azure Kubernetes Service 배포 웹 서비스에 대해 지원되며, 토큰 기반 인증은 Azure Kubernetes Service 배포에 **만** 사용할 수 있습니다. 인증 구성에 대한 자세한 내용은 [웹 서비스로 배포된 모델에 대한 인증 구성](how-to-authenticate-web-service.md)을 참조하세요.


#### <a name="authentication-with-keys"></a>키로 인증

배포에 대해 인증을 사용하도록 설정하면 자동으로 인증 키를 만듭니다.

* Azure Kubernetes Service에 배포할 때 인증은 기본적으로 활성화되어 있습니다.
* Azure 컨테이너 인스턴스에 배포할 때 인증은 기본적으로 비활성화되어 있습니다.

인증을 제어하려면 배포를 만들거나 업데이트할 때 `auth_enabled` 매개 변수를 사용합니다.

인증을 사용하도록 설정한 경우 `get_keys` 메서드를 사용하여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성해야 하는 경우 [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)를 사용합니다.

#### <a name="authentication-with-tokens"></a>토큰으로 인증

웹 서비스에 토큰 인증을 사용하도록 설정하는 경우, 웹 서비스에서 액세스할 수 있도록 사용자가 Azure Machine Learning JWT 토큰을 제공해야 합니다. 

* Azure Kubernetes Service에 배포하는 경우 토큰 인증이 기본값으로 사용하지 않도록 설정됩니다.
* Azure Container Instances에 배포하는 경우 토큰 인증이 지원되지 않습니다.

토큰 인증을 제어하려면 배포를 만들거나 업데이트할 때 `token_auth_enabled` 매개 변수를 사용합니다.

토큰 인증을 사용하도록 설정하면 `get_token` 메서드를 사용하여 전달자 토큰 및 이 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

[Azure CLI 및 기계 학습 확장이](reference-azure-machine-learning-cli.md)있는 경우 다음 명령을 사용하여 토큰을 가져올 수 있습니다.

```azurecli
az ml service get-access-token -n <service-name>
```

> [!IMPORTANT]
> 현재 토큰을 검색하는 유일한 방법은 Azure Machine Learning SDK 또는 Azure CLI Machine Learning 확장을 사용하는 것입니다.

토큰의 `refresh_by` 시간 후 새 토큰을 요청해야 합니다. 

## <a name="request-data"></a>요청 데이터

REST API는 요청 본문이 다음과 같은 구조의 JSON 문서가 될 것으로 예상합니다.

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

> [!IMPORTANT]
> 데이터의 구조는 서비스의 채점 스크립트 및 모델이 예상하는 것과 일치해야 합니다. 채점 스크립트는 모델에 데이터를 전달하기 전에 데이터를 수정할 수 있습니다.

### <a name="binary-data"></a>이진 데이터

서비스에서 이진 데이터에 대한 지원을 사용하도록 설정하는 방법에 대한 자세한 내용은 [이진 데이터](how-to-deploy-advanced-entry-script.md#binary-data)를 참조하세요.

> [!TIP]
> 배포된 모델에서 사용하는 score.py 파일에서 이진 데이터에 대한 지원을 사용하도록 설정합니다. 클라이언트에서 프로그래밍 언어의 HTTP 기능을 사용합니다. 예를 들어 다음 코드 조각은 JPG 파일의 내용을 웹 서비스로 보냅니다.
>
> ```python
> import requests
> # Load image data
> data = open('example.jpg', 'rb').read()
> # Post raw data to scoring URI
> res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/> octet-stream'})
> ```

### <a name="cross-origin-resource-sharing-cors"></a>CORS(원본 간 리소스 공유)

서비스에서 CORS 지원을 사용하도록 설정하는 방법에 대한 자세한 내용은 [원본 간 리소스 공유](how-to-deploy-advanced-entry-script.md#cors)를 참조하세요.

## <a name="call-the-service-c"></a>서비스 호출(C#)

이 예제에서는 C#을 사용하여 [노트북 내에서 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) 예제에서 생성된 웹 서비스를 호출하는 방법을 보여줍니다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using Newtonsoft.Json;

namespace MLWebServiceClient
{
    // The data structure expected by the service
    internal class InputData
    {
        [JsonProperty("data")]
        // The service used by this example expects an array containing
        //   one or more arrays of doubles
        internal double[,] data;
    }
    class Program
    {
        static void Main(string[] args)
        {
            // Set the scoring URI and authentication key or token
            string scoringUri = "<your web service URI>";
            string authKey = "<your key or token>";

            // Set the data to be sent to the service.
            // In this case, we are sending two sets of data to be scored.
            InputData payload = new InputData();
            payload.data = new double[,] {
                {
                    0.0199132141783263,
                    0.0506801187398187,
                    0.104808689473925,
                    0.0700725447072635,
                    -0.0359677812752396,
                    -0.0266789028311707,
                    -0.0249926566315915,
                    -0.00259226199818282,
                    0.00371173823343597,
                    0.0403433716478807
                },
                {
                    -0.0127796318808497, 
                    -0.044641636506989, 
                    0.0606183944448076, 
                    0.0528581912385822, 
                    0.0479653430750293, 
                    0.0293746718291555, 
                    -0.0176293810234174, 
                    0.0343088588777263, 
                    0.0702112981933102, 
                    0.00720651632920303
                }
            };

            // Create the HTTP client
            HttpClient client = new HttpClient();
            // Set the auth header. Only needed if the web service requires authentication.
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authKey);

            // Make the request
            try {
                var request = new HttpRequestMessage(HttpMethod.Post, new Uri(scoringUri));
                request.Content = new StringContent(JsonConvert.SerializeObject(payload));
                request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                var response = client.SendAsync(request).Result;
                // Display the response from the web service
                Console.WriteLine(response.Content.ReadAsStringAsync().Result);
            }
            catch (Exception e)
            {
                Console.Out.WriteLine(e.Message);
            }
        }
    }
}
```

반환되는 결과는 JSON 문서와 유사합니다.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-go"></a>서비스 호출(Go)

이 예제에서는 Go를 사용하여 [노트북 내에서 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) 예제에서 생성된 웹 서비스를 호출하는 방법을 보여줍니다.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
)

// Features for this model are an array of decimal values
type Features []float64

// The web service input can accept multiple sets of values for scoring
type InputData struct {
    Data []Features `json:"data",omitempty`
}

// Define some example data
var exampleData = []Features{
    []float64{
        0.0199132141783263, 
        0.0506801187398187, 
        0.104808689473925, 
        0.0700725447072635, 
        -0.0359677812752396, 
        -0.0266789028311707, 
        -0.0249926566315915, 
        -0.00259226199818282, 
        0.00371173823343597, 
        0.0403433716478807,
    },
    []float64{
        -0.0127796318808497, 
        -0.044641636506989, 
        0.0606183944448076, 
        0.0528581912385822, 
        0.0479653430750293, 
        0.0293746718291555, 
        -0.0176293810234174, 
        0.0343088588777263, 
        0.0702112981933102, 
        0.00720651632920303,
    },
}

// Set to the URI for your service
var serviceUri string = "<your web service URI>"
// Set to the authentication key or token (if any) for your service
var authKey string = "<your key or token>"

func main() {
    // Create the input data from example data
    jsonData := InputData{
        Data: exampleData,
    }
    // Create JSON from it and create the body for the HTTP request
    jsonValue, _ := json.Marshal(jsonData)
    body := bytes.NewBuffer(jsonValue)

    // Create the HTTP request
    client := &http.Client{}
    request, err := http.NewRequest("POST", serviceUri, body)
    request.Header.Add("Content-Type", "application/json")

    // These next two are only needed if using an authentication key
    bearer := fmt.Sprintf("Bearer %v", authKey)
    request.Header.Add("Authorization", bearer)

    // Send the request to the web service
    resp, err := client.Do(request)
    if err != nil {
        fmt.Println("Failure: ", err)
    }

    // Display the response received
    respBody, _ := ioutil.ReadAll(resp.Body)
    fmt.Println(string(respBody))
}
```

반환되는 결과는 JSON 문서와 유사합니다.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-java"></a>서비스 호출(Java)

이 예제에서는 Java를 사용하여 [노트북 내에서 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) 예제에서 생성된 웹 서비스를 호출하는 방법을 보여줍니다.

```java
import java.io.IOException;
import org.apache.http.client.fluent.*;
import org.apache.http.entity.ContentType;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;

public class App {
    // Handle making the request
    public static void sendRequest(String data) {
        // Replace with the scoring_uri of your service
        String uri = "<your web service URI>";
        // If using authentication, replace with the auth key or token
        String key = "<your key or token>";
        try {
            // Create the request
            Content content = Request.Post(uri)
            .addHeader("Content-Type", "application/json")
            // Only needed if using authentication
            .addHeader("Authorization", "Bearer " + key)
            // Set the JSON data as the body
            .bodyString(data, ContentType.APPLICATION_JSON)
            // Make the request and display the response.
            .execute().returnContent();
            System.out.println(content);
        }
        catch (IOException e) {
            System.out.println(e);
        }
    }
    public static void main(String[] args) {
        // Create the data to send to the service
        JSONObject obj = new JSONObject();
        // In this case, it's an array of arrays
        JSONArray dataItems = new JSONArray();
        // Inner array has 10 elements
        JSONArray item1 = new JSONArray();
        item1.add(0.0199132141783263);
        item1.add(0.0506801187398187);
        item1.add(0.104808689473925);
        item1.add(0.0700725447072635);
        item1.add(-0.0359677812752396);
        item1.add(-0.0266789028311707);
        item1.add(-0.0249926566315915);
        item1.add(-0.00259226199818282);
        item1.add(0.00371173823343597);
        item1.add(0.0403433716478807);
        // Add the first set of data to be scored
        dataItems.add(item1);
        // Create and add the second set
        JSONArray item2 = new JSONArray();
        item2.add(-0.0127796318808497);
        item2.add(-0.044641636506989);
        item2.add(0.0606183944448076);
        item2.add(0.0528581912385822);
        item2.add(0.0479653430750293);
        item2.add(0.0293746718291555);
        item2.add(-0.0176293810234174);
        item2.add(0.0343088588777263);
        item2.add(0.0702112981933102);
        item2.add(0.00720651632920303);
        dataItems.add(item2);
        obj.put("data", dataItems);

        // Make the request using the JSON document string
        sendRequest(obj.toJSONString());
    }
}
```

반환되는 결과는 JSON 문서와 유사합니다.

```json
[217.67978776218715, 224.78937091757172]
```

## <a name="call-the-service-python"></a>서비스 호출(Python)

이 예제에서는 Python을 사용하여 [노트북 내에서 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/notebook_runner/training_notebook.ipynb) 예제에서 생성된 웹 서비스를 호출하는 방법을 보여줍니다.

```python
import requests
import json

# URL for the web service
scoring_uri = '<your web service URI>'
# If the service is authenticated, set the key or token
key = '<your key or token>'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [
                0.0199132141783263,
                0.0506801187398187,
                0.104808689473925,
                0.0700725447072635,
                -0.0359677812752396,
                -0.0266789028311707,
                -0.0249926566315915,
                -0.00259226199818282,
                0.00371173823343597,
                0.0403433716478807
            ],
            [
                -0.0127796318808497,
                -0.044641636506989,
                0.0606183944448076,
                0.0528581912385822,
                0.0479653430750293,
                0.0293746718291555,
                -0.0176293810234174,
                0.0343088588777263,
                0.0702112981933102,
                0.00720651632920303]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}
# If authentication is enabled, set the authorization header
headers['Authorization'] = f'Bearer {key}'

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

반환되는 결과는 JSON 문서와 유사합니다.

```JSON
[217.67978776218715, 224.78937091757172]
```


## <a name="web-service-schema-openapi-specification"></a>웹 서비스 스키마(OpenAPI 사양)

배포에서 자동 스키마 생성을 사용하는 경우 [swagger_uri 속성](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri)을 사용하여 서비스에 대한 OpenAPI 사양의 주소를 가져올 수 있습니다. (예: `print(service.swagger_uri)`.) GET 요청을 사용하거나 브라우저에서 URI를 열어 사양을 검색합니다.

다음 JSON 문서는 배포에 대해 생성된 스키마(OpenAPI 사양)의 예제입니다.

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

자세한 내용은 [OpenAPI 사양](https://swagger.io/specification/)을 참조하세요.

사양에서 클라이언트 라이브러리를 만들 수 있는 유틸리티는 [swagger-codegen](https://github.com/swagger-api/swagger-codegen)를 참조하세요.


> [!TIP]
> 서비스를 배포한 후 스키마 JSON 문서를 검색할 수 있습니다. 배포된 웹 서비스의 [swagger_uri 속성](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#swagger-uri) (예: `service.swagger_uri`)을 사용하여 로컬 웹 서비스의 swagger 파일에 대한 URI를 가져옵니다.

## <a name="consume-the-service-from-power-bi"></a>Power BI에서 서비스 사용

Power BI에서는 Azure Machine Learning 웹 서비스를 사용하여 예측을 통해 Power BI 데이터를 지원합니다. 

Power BI에서 사용하도록 지원되는 웹 서비스를 생성하려면 스키마가 Power BI에 필요한 형식을 지원해야 합니다. [Power BI 지원 스키마를 만드는 방법에 대해 알아봅니다](./how-to-deploy-advanced-entry-script.md#power-bi-compatible-endpoint).

웹 서비스가 배포되면 Power BI 데이터 흐름에서 사용할 수 있습니다. [Power BI에서 Azure Machine Learning 웹 서비스를 사용하는 방법을 알아봅니다](/power-bi/service-machine-learning-integration).

## <a name="next-steps"></a>다음 단계

Python 및 딥 러닝 모델에 대한 실시간 채점을 위한 참조 아키텍처를 보려면 [Azure 아키텍처 센터](/azure/architecture/reference-architectures/ai/realtime-scoring-python)로 이동하세요.
