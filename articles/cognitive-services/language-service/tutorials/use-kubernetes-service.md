---
title: Azure Kubernetes Service에 키 구 추출 컨테이너 배포
titleSuffix: Azure Cognitive Services
description: Azure Kubernetes Service에 키 구 추출 컨테이너 이미지를 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a71910bde7718c0a60eda87ea51f6c49e473304a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102821"
---
# <a name="deploy-a-key-phrase-extraction-container-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 키 구 추출 컨테이너 배포

Azure Kubernetes 서비스 (AKS)에 [키 문구 추출 Docker 컨테이너](../key-phrase-extraction/how-to/use-containers.md) 이미지를 배포 하는 방법에 대해 알아봅니다. 이 절차에서는 언어 리소스를 만드는 방법, 컨테이너 이미지를 연결 하는 방법 및 브라우저에서 두 작업의 오케스트레이션을 실행 하는 방법을 보여 줍니다. 컨테이너를 사용하면 인프라 관리에 대한 주의를 돌려 애플리케이션 개발에 집중할 수 있습니다. 이 문서에서는 키 구 추출 컨테이너를 예로 사용 하지만, Azure 인식 서비스에서 제공 하는 언어에 대해이 프로세스를 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell은 사용하지 마세요. 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다.
* 텍스트 편집기(예: [Visual Studio Code](https://code.visualstudio.com/download)).
* [Azure CLI](/cli/azure/install-azure-cli)가 설치되어 있음.
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)가 설치되어 있음.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층을 사용 하는 **Azure 언어** 리소스
    * S0 가격 책정 계층이 있는 **Azure Cognitive Services** 리소스.

[!INCLUDE [Create a Cognitive Services Language resource](../includes/containers/create-text-analytics-resource.md)]

[!INCLUDE [Create a language container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]


## <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>AKS 클러스터에 핵심 구 추출 컨테이너 배포

1. Azure CLI를 열고 Azure에 로그인합니다.

    ```azurecli
    az login
    ```

1. AKS 클러스터에 로그인합니다. `your-cluster-name` 및 `your-resource-group`를 해당 값으로 바꿉니다.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    이 명령을 실행하면 다음과 유사한 메시지가 보고됩니다.

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Azure 계정에서 여러 구독을 사용할 수 있고 `az aks get-credentials` 명령이 오류와 함께 반환되는 경우 일반적인 문제는 잘못된 구독을 사용하고 있는 것입니다. 리소스를 만든 것과 동일한 구독을 사용하도록 Azure CLI 세션의 컨텍스트를 설정하고 다시 시도하세요.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 선택한 텍스트 편집기를 엽니다. 이 예제에서는 Visual Studio Code를 사용합니다.

    ```console
    code .
    ```

1. 텍스트 편집기 내에서 *keyphrase.yaml* 이라는 새 파일을 만들고 다음 YAML을 붙여넣습니다. `billing/value` 및 `apikey/value`를 사용자 고유의 정보로 바꿉니다.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. 파일을 저장하고 텍스트 편집기를 닫습니다.
1. *keyphrase.yaml* 파일을 대상으로 하여 Kubernetes `apply` 명령을 실행합니다.

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    명령이 배포 구성을 성공적으로 적용하면 다음 출력과 비슷한 메시지가 표시됩니다.

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Pod가 배포되었는지 확인합니다.

    ```console
    kubectl get pods
    ```

    Pod의 실행 상태에 대한 출력:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 서비스를 사용할 수 있는지 확인하고 IP 주소를 가져옵니다.

    ```console
    kubectl get services
    ```

    Pod에서 *keyphrase* 서비스의 실행 상태에 대한 출력:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

## <a name="verify-the-key-phrase-extraction-container-instance"></a>핵심 구 추출 컨테이너 인스턴스 확인

1. **개요** 탭을 선택하고 IP 주소를 복사합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력합니다. 예를 들어 `http://<IP-address>:5000 (http://55.55.55.55:5000`을 입력합니다. 컨테이너가 실행 중임을 알려주는 컨테이너의 홈페이지가 표시됩니다.

    ![컨테이너 홈페이지를 확인하여 실행 중인지 확인합니다.](../media/swagger-container-documentation.png)

1. **서비스 API 설명** 링크를 선택하여 컨테이너의 Swagger 페이지로 이동합니다.

1. **POST** API 중 하나를 선택하고 **사용해 보기** 를 선택합니다. 이 예제 입력을 포함하는 매개 변수가 표시됩니다.

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 입력을 다음 JSON 콘텐츠로 바꿉니다.

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. **showStats** 를 `true`로 설정합니다.

1. **실행** 을 선택하여 텍스트의 감정을 확인합니다.

    컨테이너에 패키징된 모델은 0에서 1 사이의 점수를 생성합니다. 여기서 0은 부정적이고 1은 긍정적입니다.

    반환되는 JSON 응답에는 업데이트된 텍스트 입력에 대한 감정이 포함됩니다.

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
          "statistics": {
            "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

이제 응답 페이로드의 JSON 데이터 문서 `id`를 원래 요청 페이로드 문서 `id`와 연관시킬 수 있습니다. 결과 문서에는 해당 입력 문서에서 추출된 핵심 구 목록이 포함된 `keyPhrases` 배열이 있습니다. 또한 결과 문서마다 `characterCount`, `transactionCount` 등의 다양한 통계가 있습니다.

## <a name="next-steps"></a>다음 단계

* 추가적인 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* [핵심 구 추출 개요](../overview.md)
