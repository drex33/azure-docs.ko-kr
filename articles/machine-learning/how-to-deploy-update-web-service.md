---
title: 웹 서비스 업데이트
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 이미 배포된 웹 서비스를 새로 고치는 방법에 대해 알아봅니다. 모델, 환경, 항목 스크립트 등의 설정을 업데이트할 수 있습니다.
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: ed3b0512a7f73ae9e387b41871e2ee59607e731d
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597501"
---
# <a name="update-a-deployed-web-service"></a>배포된 웹 서비스 업데이트

이 문서에서는 Azure Machine Learning을 사용하여 배포된 웹 서비스를 업데이트하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 Azure Machine Learning을 사용하여 웹 서비스를 이미 배포했다고 가정합니다. 웹 서비스를 배포하는 방법을 알아보려면 [다음 단계](how-to-deploy-and-where.md)를 따르세요.

## <a name="update-web-service"></a>웹 서비스 업데이트

웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 유추 구성에서 지정할 수 있는 새 모델, 새 항목 스크립트 또는 새 종속성을 사용하도록 웹 서비스를 업데이트할 수 있습니다. 자세한 내용은 [Webservice.update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-)에 대한 설명서를 참조하세요.

[AKS Service 업데이트 메서드](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)를 참조합니다.

[ACI Service 업데이트 메서드](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)를 참조합니다.

> [!IMPORTANT]
> 모델의 새 버전을 만들 때 이를 사용할 각 서비스를 수동으로 업데이트해야 합니다.
>
> Azure Machine Learning 디자이너로부터 게시된 웹 서비스는 SDK를 사용하여 업데이트할 수 없습니다.

> [!IMPORTANT]
> Azure Kubernetes Service는 버전 1.16 이상의 [Blobfuse FlexVolume 드라이버](https://github.com/Azure/kubernetes-volume-drivers/blob/master/flexvolume/blobfuse/README.md)와 버전 1.17 이상의 [Blob CSI 드라이버](https://github.com/kubernetes-sigs/blob-csi-driver/blob/master/README.md)를 사용합니다. 
>
> 따라서 클러스터 버전에 올바른 blobfuse 방법으로 배포하려면 클러스터 업그레이드 후 웹 서비스를 다시 배포하거나 업데이트하는 것이 중요합니다.

> [!NOTE]
> 작업이 이미 진행 중인 경우 동일한 웹 서비스에 대한 새 작업은 모두 409 충돌 오류로 응답합니다. 예를 들어, 웹 서비스 만들기 또는 업데이트 작업이 진행 중인데 새 삭제 작업을 트리거하면 오류가 발생합니다.

**SDK 사용**

다음 코드에서는 SDK를 사용하여 웹 서비스의 모델, 환경 및 항목 스크립트를 업데이트하는 방법을 보여 줍니다.

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**CLI 사용**

ML CLI를 사용하여 웹 서비스를 업데이트할 수도 있습니다. 다음 예제에서는 새 모델을 등록한 다음 이를 사용하도록 웹 서비스를 업데이트하는 방법을 보여 줍니다.

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 이 예제에서는 JSON 문서를 사용하여 등록 명령의 모델 정보를 업데이트 명령에 전달합니다.
>
> 새 항목 스크립트나 환경을 사용하도록 서비스를 업데이트하려면 [유추 구성 파일](./reference-azure-machine-learning-cli.md#inference-configuration-schema)을 만들고 `ic` 매개 변수를 사용하여 해당 파일을 지정합니다.

자세한 내용은 [az ml service update](/cli/azure/ml(v1)/service?view=azure-cli-latest#az_ml_v1__service_update) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용하는 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)
* [사용자 지정 Docker 이미지를 사용하여 모델을 배포하는 방법](./how-to-deploy-custom-container.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
