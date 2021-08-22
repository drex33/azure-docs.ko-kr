---
title: FPGA에 ML 모델 배포
titleSuffix: Azure Machine Learning
description: 필드 프로그래머블 게이트 어레이에 대해 알아봅니다. 대기 시간이 매우 짧은 추론을 위해 Azure Machine Learning을 사용하여 FPGA에서 웹 서비스를 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-python, deploy
ms.openlocfilehash: bfb29a00dbef95007eba6ca69f9a38d0f23d2da2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528711"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 ML 모델을 FPGA(필드 프로그래머블 게이트 어레이)에 배포 

이 문서에서는 [Azure Machine Learning](overview-what-is-azure-machine-learning.md)에서 [하드웨어 가속 모델 Python 패키지](/python/api/azureml-accel-models/azureml.accel)를 사용하여 Azure FPGA에 ML 모델을 배포하는 방법 및 FPGA에 대해 알아봅니다.

## <a name="what-are-fpgas"></a>FPGA란?
FPGA는 프로그래밍 가능한 논리 블록 배열과 재구성 가능한 상호 연결 계층 구조를 포함하고 있습니다. 제조 후 상호 연결을 통해 이러한 블록을 다양한 방법으로 구성할 수 있습니다. 다른 칩과 비교해서, FPGA는 프로그래밍 기능 및 성능 조합을 제공합니다. 

FPGA를 사용하면 실시간 유추(또는 모델 채점) 요청을 위한 짧은 대기 시간을 달성할 수 있습니다. 비동기 요청(일괄 처리)이 필요하지 않습니다. 일괄 처리를 사용하면 더 많은 데이터를 처리해야 하므로 대기 시간이 길어질 수 있습니다. 인공신경망 장치 구현에는 일괄 처리가 필요 없으며, 따라서 CPU 및 GPU 프로세서에 비해 대기 시간을 몇 배나 줄일 수 있습니다.

여러 형식의 기계 학습 모델에 대해 FPGA를 다시 구성할 수 있습니다. 이러한 유연성 덕분에 사용되는 최적의 숫자 정밀도 및 메모리 모델에 따라 애플리케이션을 가속화할 수 있습니다. FPGA는 다시 구성할 수 있으므로 빠르게 변화하는 AI 알고리즘의 요구 사항에 맞게 최신 상태를 유지할 수 있습니다.

![Azure Machine Learning FPGA 비교 다이어그램](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|프로세서| 약어 |설명|
|---|:-------:|------|
|애플리케이션 관련 집적 회로|ASIC|Google의 TPU(Tensor Processor Units) 같은 사용자 지정 회로는 가장 높은 효율성을 제공합니다. 이러한 회로는 변하는 요구 사항에 따라 재구성할 수 없습니다.|
|Field-programmable Gate Arrays|FPGA|Azure에서 사용할 수 있는 것과 같은 FPGA는 ASIC에 가까운 성능을 제공합니다. 또한 유연하고, 시간 경과에 따라 새 논리를 구현하기 위해 다시 구성할 수 있습니다.|
|그래픽 처리 장치|GPU|GPU는 AI 계산에 널리 사용되며, CPU보다 이미지 렌더링 속도가 빠른 병렬 처리 기능을 제공합니다.|
|중앙 처리 장치|CPU|범용 프로세서이며, 성능이 그래픽 및 비디오 처리에 적합하지 않습니다.|

## <a name="fpga-support-in-azure"></a>Azure의 FPGA 지원

Microsoft Azure는 FPGA 부문에서 세계 최대의 클라우드 투자 규모를 자랑합니다. Microsoft에서는 DNN(심층 신경망) 평가, Bing 검색 순위, SDN(소프트웨어 정의 네트워킹) 가속화에 FPGA를 사용하여 대기 시간을 줄이는 한편, CPU 리소스를 회수하여 다른 작업에 사용합니다.

Azure의 FPGA는 데이터 과학자 및 개발자가 실시간 AI 계산을 가속화하는 데 사용하는 Intel의 FPGA 디바이스를 기반으로 합니다. 이 FPGA 지원 아키텍처는 강력한 성능, 유연성 및 확장성을 제공하며 Azure에서 사용할 수 있습니다.

Azure FPGA는 Azure Machine Learning과 통합됩니다. Azure는 미리 학습된 DNN을 FPGA 전반에서 병렬로 실행하여 서비스를 스케일 아웃할 수 있습니다. DNN은 전송 학습을 위한 심층 기능 기능화기로 미리 학습하거나 업데이트된 가중치를 사용하여 미세 조정할 수 있습니다.

|Azure의 시나리오 및 구성|지원되는 DNN 모델|지역 지원|
|--------------------------|--------------------|----------------|
|+ 이미지 분류 및 인식 시나리오<br/>+ TensorFlow 배포(Tensorflow 1.x 필요)<br/>+ Intel FPGA 하드웨어|- ResNet 50<br/>- ResNet 152<br/>- DenseNet-121<br/>- VGG-16<br/>- SSD-VGG|- 미국 동부<br/>- 동남 아시아<br/> - 서유럽<br/>- 미국 서부 2|

대기 시간 및 처리량을 최적화하기 위해 FPGA 모델에 데이터를 보내는 클라이언트는 위의 영역 중 하나(모델을 배포한 항목)에 있어야 합니다.

**Azure VM의 PBS 패밀리** 에는 Intel Arria 10 FPGA가 포함됩니다. Azure 할당량 할당을 확인하는 경우 "표준 PBS 제품군 vCPU"로 표시됩니다. PB6 VM에는 6개의 vCPU와 하나의 FPGA가 있습니다. PB6 VM은 모델을 FPGA에 배포하는 동안 Azure Machine Learning에 의해 자동으로 프로비전됩니다. Azure ML에서만 사용되며 임의의 비트스트림을 실행할 수 없습니다. 예를 들어 비트스트림을 사용하여 FPGA를 플래시하여 암호화, 인코딩 등을 수행할 수 없습니다.

## <a name="deploy-models-on-fpgas"></a>FPGA에 모델 배포

[Azure Machine Learning 하드웨어 가속 모델](/python/api/azureml-accel-models/azureml.accel)을 사용하여 모델을 FPGA에서 웹 서비스로 배포할 수 있습니다. FPGA를 사용하면 단일 배치 크기로도 대기 시간이 엄청나게 짧은 추론이 가능합니다. 

이 예제에서는 TensorFlow 그래프를 만들어 입력 이미지를 전처리하고, FPGA에서 ResNet 50을 사용하여 기능화기를 만든 다음, ImageNet 데이터 세트에 대해 학습된 분류자를 통해 기능을 실행합니다. 그런 다음, AKS 클러스터에 모델을 배포합니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 없는 경우 [종량제](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) 계정을 만듭니다(무료 Azure 계정은 FPGA 할당량에 적합하지 않음).

- Azure Machine Learning 작업 영역 및 [작업 영역 만들기](how-to-manage-workspace.md)에 설명된 대로 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다.
 
- 하드웨어 가속 모델 패키지: `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](/cli/azure/install-azure-cli)

- FPGA 할당량 [할당량에 대한 요청](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)을 제출하거나 이 CLI 명령을 실행하여 할당량을 확인합니다. 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   반환된 __CurrentValue__ 아래에 최소 6개의 vCPU가 있는지 확인합니다.  

### <a name="define-the-tensorflow-model"></a>TensorFlow 모델 정의

[Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)를 사용하여 시작하여 서비스 정의를 만듭니다. 서비스 정의는 TensorFlow를 기반으로 그래프(입력, 기능화기 및 분류자) 파이프라인을 설명하는 파일입니다. 배포 명령은 정의 및 그래프를 ZIP 파일로 압축하고, ZIP 파일을 Azure Blob 스토리지에 업로드합니다. DNN은 이미 배포되어 FPGA에서 실행되고 있습니다.

1. Azure Machine Learning 작업 영역 로드

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. 이미지를 전처리합니다. 웹 서비스에 대한 입력은 JPEG 이미지입니다.  첫 번째 단계는 JPEG 이미지를 디코딩하고 전처리하는 것입니다.  JPEG 이미지는 문자열로 처리되고 결과는 텐서이며 이는 ResNet 50 모델에 대한 입력입니다.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. 기능화기를 로드합니다. 모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.  다른 심층 신경망을 가져오기 위해 코드 조각에서 "QuantizedResnet50"을 바꿉니다.

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. 분류자를 추가합니다. 이 분류자는 ImageNet 데이터 세트를 기반으로 학습되었습니다.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. 모델을 저장합니다. 이제 전처리기, ResNet 50 기능화기 및 분류자가 로드되었으므로 그래프 및 연관된 변수를 모델로 저장합니다.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. **입력 및 출력 텐서를 모델 변환 및 유추 요청에 사용할 수 있도록** 저장합니다. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   기본 분류자를 사용하는 경우 유추를 위한 분류자 출력 텐서와 함께 사용할 수 있는 모델은 다음과 같습니다.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>모델을 ONNX(Open Neural Network Exchange) 형식으로 변환합니다.

FPGA에 배포하기 전에 모델을 [ONNX](https://onnx.ai/) 형식으로 변환합니다.

1. Azure Blob 스토리지에서 ZIP 파일과 함께 SDK를 사용하여 모델을 [등록](concept-model-management-and-deployment.md)합니다. 모델에 대한 태그 및 기타 메타데이터를 추가하면 학습된 모델을 추적하는 데 도움이 됩니다.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   이미 모델을 등록했고 로드하려는 경우 모델을 검색할 수 있습니다.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. TensorFlow 그래프를 ONNX 형식으로 변환합니다.  클라이언트에서 웹 서비스를 사용할 때 사용할 수 있도록 입력 및 출력 텐서의 이름을 제공해야 합니다.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

### <a name="containerize-and-deploy-the-model"></a>모델 컨테이너화 및 배포

다음으로 변환된 모델 및 모든 종속성에서 Docker 이미지를 만듭니다.  그런 다음, 이 Docker 이미지를 배포하고 인스턴스화할 수 있습니다.  지원되는 배포 대상은 클라우드의 AKS(Azure Kubernetes Service) 또는 [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md)와 같은 에지 디바이스를 포함합니다.  등록된 Docker 이미지에 대한 태그 및 설명을 추가할 수도 있습니다.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   태그로 이미지를 나열하고 디버깅에 대한 자세한 로그를 가져옵니다.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 배포

1. 모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS를 사용합니다. Azure Machine Learning SDK, CLI 또는 [Azure Machine Learning 스튜디오](https://ml.azure.com)를 사용하여 새 항목을 만들 수 있습니다.

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    AKS 배포는 약 15분 정도 걸릴 수 있습니다.  배포가 성공했는지 확인합니다.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. AKS 클러스터에 컨테이너를 배포합니다.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

#### <a name="deploy-to-a-local-edge-server"></a>로컬 에지 서버에 배포

모든 [Azure Data Box Edge 디바이스](../databox-online/azure-stack-edge-overview.md
)에는 모델을 실행하기 위한 FPGA가 포함되어 있습니다.  한 번에 하나의 모델만 FPGA에서 실행할 수 있습니다.  다른 모델을 실행하려면 새 컨테이너를 배포하기만 하면 됩니다. 지침 및 샘플 코드는 [이 Azure 샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)에서 찾을 수 있습니다.

### <a name="consume-the-deployed-model"></a>배포된 모델 사용

마지막으로 샘플 클라이언트를 사용하여 Docker 이미지를 호출하여 모델에서 예측을 가져옵니다.  샘플 클라이언트 코드를 사용할 수 있습니다.
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker 이미지는 gRPC와 TensorFlow Serving "predict" API을 지원합니다.

TensorFlow Serving에 대한 샘플 클라이언트를 다운로드할 수도 있습니다.

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

이 분류자는 [ImageNet](http://www.image-net.org/) 데이터 세트에 대해 학습되었으므로 클래스를 사람이 읽을 수 있는 레이블에 매핑합니다.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-resources"></a>리소스 정리

불필요한 비용을 방지하려면 **다음 순서로**(웹 서비스, 이미지, 모델) 리소스를 정리합니다.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>다음 단계

+ [웹 서비스를 보호](how-to-secure-web-service.md)하는 방법 문서에 대해 알아봅니다.

+ FPGA 및 [Azure Machine Learning 가격 책정 및 비용](https://azure.microsoft.com/pricing/details/machine-learning/)에 대해 알아보세요.

+ [하이퍼스케일 하드웨어: Azure + FPGA 상단에 있는 대규모 ML: 빌드 2018(비디오)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Microsoft FPGA 기반 구성 가능 클라우드(비디오)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [실시간 AI용 Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/)

+ [자동화된 광학 검사 시스템](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
