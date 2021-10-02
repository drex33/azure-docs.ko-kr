---
title: 클라우드 지역 간 기능 가용성
titleSuffix: Azure Machine Learning
description: 이 문서는 퍼블릭 클라우드와 Azure Government, Azure 독일 및 Azure 중국 21Vianet 지역 간의 기능 가용성 차이점을 나열합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2021
ms.custom: references_regions
ms.openlocfilehash: 8c05b77a77bbcbfc977dadb22151f0086001b859
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359447"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Azure Machine Learning 클라우드 지역 간 기능 가용성

Azure Government, Azure 독일 및 Azure 중국 21Vianet 지역에서 사용할 수 있는 Azure Machine Learning 기능에 대해 알아봅니다. 

글로벌 Azure 지역 목록에는 퍼블릭 클라우드 지역 외에도 특정 시장에 서비스를 제공하는 여러 지역이 있습니다. 예를 들어 Azure Government 및 Azure 중국 21Vianet 지역이 있습니다. Azure Machine Learning은 퍼블릭 클라우드 지역 외에도 다음 지역에 배포됩니다.

* Azure Government 지역 **미국 애리조나** 및 **미국 버지니아**
* Azure 중국 21Vianet 지역 **중국 동부 2**

이 문서의 나머지 부분에서는 이러한 지역에서 사용할 수 있는 Azure Machine Learning 기능에 대한 정보와 함께 이러한 기능 사용에 대한 지역별 정보를 제공합니다.
## <a name="azure-government"></a>Azure Government 

| 기능 | 퍼블릭 클라우드 상태  | 미국 버지니아 | 미국 애리조나| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[자동화된 기계 학습](concept-automated-ml.md)** | | | |
| Notebook에서 실험 만들기 및 실행                                    | GA                   | YES                | YES         |
| 스튜디오 웹 환경에서 실험 만들기 및 실행                        | 공개 미리 보기       | YES                | YES         |
| 업계 최고의 예측 기능                                  | GA                   | YES                | YES         |
| 딥 러닝 및 기타 고급 학습자 지원                      | GA                   | YES                | YES         |
| 대량 데이터 지원(최대 100GB)                                          | 공개 미리 보기       | YES                | YES         |
| Azure Databricks 통합                                              | GA                   | 아니요                 | 아니요          |
| SQL, CosmosDB 및 HDInsight 통합                                   | GA                   | YES                | YES         |
| **[Machine Learning 파이프라인](concept-ml-pipelines.md)** |   |  | | 
| Azure ML SDK를 사용하여 파이프라인 만들기, 실행 및 게시                   | GA                   | YES                | YES         |
| Azure ML SDK를 사용하여 파이프라인 엔드포인트 만들기                           | GA                   | YES                | YES         |
| Azure ML SDK를 사용하여 예약된 파이프라인의 실행 만들기, 편집 및 삭제 | GA                   | 예*               | 예*        |
| 스튜디오에서 파이프라인 실행 세부 정보 보기                                        | GA                   | YES                | YES         |
| Azure ML 디자이너에서 파이프라인 만들기, 실행, 시각화 및 게시          | GA      | YES                | YES         |
| ML 파이프라인과 Azure Databricks 통합                             | GA                   | 아니요                 | 아니요          |
| Azure ML 디자이너에서 파이프라인 엔드포인트 만들기                             | GA      | YES                | YES         |
| **[통합 Notebook](how-to-run-jupyter-notebooks.md)** |   |  | | 
| 작업 영역 Notebook 및 파일 공유                                        | GA                   | YES                | YES         |
| R 및 Python 지원                                                       | GA                   | YES                | YES         |
| Virtual Network 지원                                                    | 공개 미리 보기       | 아니요                 | 아니요          |
| **[컴퓨팅 인스턴스](concept-compute-instance.md)** |   |  | | 
| 통합 Notebook에 대한 관리되는 컴퓨팅 인스턴스                         | GA                   | YES                | YES         |
| Jupyter, JupyterLab 통합                                            | GA                   | YES                | YES         |
| 가상 네트워크(VNet) 지원                                             | 공개 미리 보기       | YES                | YES         |
| **SDK 지원** |  |  | | 
| [Python SDK 지원](/python/api/overview/azure/ml/)                                                         | GA                   | YES                | YES         |
| **[보안](concept-enterprise-security.md)** |   | | | 
| 학습을 위한 VNet(Virtual Network) 지원                                | GA                   | YES                | YES         |
| 유추를 위한 VNet(Virtual Network) 지원                               | GA                   | YES                | YES         |
| 엔드포인트 인증 점수 매기기                                            | 공개 미리 보기       | YES                | YES         |
| 작업 영역 프라이빗 엔드포인트                                                 | GA  |  GA  |  GA |
| VNet 뒤에 있는 ACI                                                            | 공개 미리 보기       | 아니요                 | 아니요          |
| VNet 뒤에 있는 ACR                                                            | GA       | YES                 | YES          |
| AKS 클러스터의 개인 IP                                                  | 공개 미리 보기       | 아니요                 | 아니요          |
| **컴퓨팅** |   | | |
| [작업 영역에서 할당량 관리](how-to-manage-quotas.md)                                         | GA                   | YES                | YES         |
| **[기계 학습에 대한 데이터](concept-data.md)** |   | | |
| SDK에서 데이터 세트 및 데이터 저장소 만들기, 보기 또는 편집                  | GA                   | YES                | YES         |
| UI에서 데이터 세트 및 데이터 저장소 만들기, 보기 또는 편집                   | GA                   | YES                | YES         |
| SDK에서 데이터 세트 드리프트 모니터 보기, 편집 또는 삭제                   | 공개 미리 보기       | YES                | YES         |
| UI에서 데이터 세트 드리프트 모니터 보기, 편집 또는 삭제                    | 공개 미리 보기       | YES                | YES         |
| **기계 학습 수명 주기** |   | | |
| [모델 프로파일링](how-to-deploy-profile-model.md)                                                            | GA                   | YES                | PARTIAL     |
| [Azure ML CLI 1.0](reference-azure-machine-learning-cli.md)     | GA                   | YES                | YES         |
| [FPGA 기반 하드웨어 가속 모델](how-to-deploy-fpga-web-service.md)                                     | GA                   | 아니요                 | 아니요          |
| [Visual Studio Code 통합](how-to-setup-vs-code.md)                                             | 공개 미리 보기       | 아니요                 | 아니요          |
| [Event Grid 통합](how-to-use-event-grid.md)                                                     | 공개 미리 보기       | 아니요                 | 아니요          |
| [Azure Machine Learning과 Azure Stream Analytics 통합](../stream-analytics/machine-learning-udf.md)               | 공개 미리 보기       | 아니요                 | 아니요          |
| **[이미지](how-to-create-image-labeling-projects.md) 및 [텍스트](how-to-create-text-labeling-projects.md) 레이블 지정** |   | | |
| 프로젝트 관리 포털 레이블 지정                                        | GA                   | YES                | YES         |
| 레이블 지정자 포털                                                            | GA                   | YES                | YES         |
| 개인 인력을 사용하여 레이블 지정                                          | GA                   | YES                | YES         |
| ML 지원 레이블 지정(이미지 분류 및 개체 감지)           | 공개 미리 보기       | YES                | YES         |
| **[Responsible ML](concept-responsible-ml.md)** |   | | |
| UI의 설명                                                       | 공개 미리 보기       | 아니요                 | 아니요          |
| 차등 개인 정보 SmartNoise 도구 키트                                    | OSS                  | 아니요                 | 아니요          |
| 데이터 시트를 구현하는 Azure Machine Learning의 사용자 지정 태그              | GA                   | 아니요                 | 아니요          |
| 공정성 AzureML 통합                                               | 공개 미리 보기       | 아니요                 | 아니요          |
| 해석력 SDK                                                      | GA                   | YES                | YES         |
| **학습** |   | | |
| [실험 로그 스트리밍](how-to-track-monitor-analyze-runs.md)                                              | GA                   | YES                | YES         |
| [보충 학습](how-to-use-reinforcement-learning.md)                                                     | 공개 미리 보기       | 아니요                 | 아니요          |
| [실험 UI](how-to-track-monitor-analyze-runs.md)                                                         | 공개 미리 보기                   | YES                | YES         |
| [.NET 통합 ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | GA                   | YES                | YES         |
| **유추** |   | | |
| [일괄 처리 추론](tutorial-pipeline-batch-scoring-classification.md)                                                          | GA                   | YES                | YES         |
| [FPGA를 사용하여 Data Box Edge](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | 공개 미리 보기       | 아니요                 | 아니요          |
| **기타** |   | | |
| [Open Datasets](../open-datasets/samples.md)                                                              | 공개 미리 보기       | YES                | YES         |
| [사용자 지정 Cognitive Search](how-to-deploy-model-cognitive-search.md)                                                    | 공개 미리 보기       | YES                | YES         |


### <a name="azure-government-scenarios"></a>Azure Government 시나리오

| 시나리오                                                    | 미국 버지니아 | 미국 애리조나| 제한 사항  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **일반 보안 설정** |   | | |
| 인터넷 액세스 사용 안 함/제어(인바운드 및 아웃바운드) 및 특정 VNet | PARTIAL| PARTIAL   |  | 
| 연결된 모든 리소스/서비스의 배치  | YES | YES |  |
| 미사용 암호화 및 전송 중                                                 | YES | YES |  |
| 컴퓨팅 리소스에 대한 루트 및 SSH 액세스                                          | YES | YES |  |
| 엔드포인트 보호, 패치 및 로깅을 포함하여 배포된 시스템(인스턴스, 엔드포인트 등)의 보안 유지 |  PARTIAL|  PARTIAL |VNet 뒤에 있는 ACI는 현재 사용할 수 없습니다. |                                  
| ACI/AKS 통합 사용 제어(사용 안 함/한도/제한)                    | PARTIAL| PARTIAL |VNet 뒤에 있는 ACI는 현재 사용할 수 없습니다.|
| Azure RBAC(Azure 역할 기반 액세스 제어) - 사용자 지정 역할 만들기                           | YES | YES |  |
| ML 서비스에서 사용하는 ACR 이미지에 대한 액세스 제어(Azure 제공/유지 관리 및 사용자 지정)  |PARTIAL|  PARTIAL |  |
| **일반 Machine Learning Service 사용** |  | | |
| 모델을 작성하고, 해당 모델을 학습하고, 엔드포인트로 호스트하고, 웹앱을 통해 사용하도록 개발 환경을 구축하는 기능     | YES | YES |  |
| ADLS에서 데이터를 끌어오는 기능(Data Lake Storage)                                 |YES | YES |  |
| Azure Blob Storage에서 데이터를 끌어오는 기능                                       |YES | YES |  |



### <a name="other-azure-government-limitations"></a>기타 Azure Government 제한 사항

* Azure Machine Learning 컴퓨팅 인스턴스의 경우 24시간 이상 지속되는 토큰을 새로 고치는 기능은 Azure Government에서 사용할 수 없습니다.
* 모델 프로파일링은 US 애리조나 지역에서 4개의 CPU를 지원하지 않습니다.   
* 샘플 Notebook은 공용 데이터에 액세스해야 하는 경우 Azure Government에서 작동하지 않을 수 있습니다.
* IP 주소: [필수 공용 인터넷 액세스](how-to-secure-training-vnet.md#required-public-internet-access) 지침에 사용된 CLI 명령은 IP 범위를 반환하지 않습니다. 대신 [Azure Government에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)를 사용합니다.
* 또한 예약된 파이프라인의 경우 Blob 기반 트리거 메커니즘도 제공합니다. CMK 작업 영역에 대해서는 이 메커니즘이 지원되지 않습니다. CMK 작업 영역에 대한 Blob 기반 트리거를 사용하도록 설정하려면 추가 설치를 수행해야 합니다. 자세한 내용은 [논리 앱에서 기계 학습 파이프라인 실행 트리거](how-to-trigger-published-pipeline.md)를 참조하세요.
* 방화벽: Azure Government 영역을 사용하는 경우 방화벽 설정에 다음과 같은 호스트를 추가합니다.

    * 애리조나의 경우 `usgovarizona.api.ml.azure.us` 사용
    * 버지니아의 경우 `usgovvirginia.api.ml.azure.us` 사용
    * 둘 모두의 경우: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure 중국 21Vianet 

| 기능                                       | 퍼블릭 클라우드 상태 | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **자동화된 기계 학습** |    | | |
| Notebook에서 실험 만들기 및 실행                                    | GA               | YES       | 해당 없음        |
| 스튜디오 웹 환경에서 실험 만들기 및 실행                        | 공개 미리 보기   | YES       | 해당 없음        |
| 업계 최고의 예측 기능                                  | GA               | YES       | 해당 없음        |
| 딥 러닝 및 기타 고급 학습자 지원                      | GA               | YES       | 해당 없음        |
| 대량 데이터 지원(최대 100GB)                                          | 공개 미리 보기   | YES       | 해당 없음        |
| Azure Databricks 통합                                              | GA               | 아니요        | 해당 없음        |
| SQL, CosmosDB 및 HDInsight 통합                                   | GA               | YES       | 해당 없음        |
| **Machine Learning 파이프라인** |    | | |
| Azure ML SDK를 사용하여 파이프라인 만들기, 실행 및 게시                   | GA               | YES       | 해당 없음        |
| Azure ML SDK를 사용하여 파이프라인 엔드포인트 만들기                           | GA               | YES       | 해당 없음        |
| Azure ML SDK를 사용하여 예약된 파이프라인의 실행 만들기, 편집 및 삭제 | GA               | YES       | 해당 없음        |
| 스튜디오에서 파이프라인 실행 세부 정보 보기                                        | GA               | YES       | 해당 없음        |
| Azure ML 디자이너에서 파이프라인 만들기, 실행, 시각화 및 게시          | GA  | YES       | 해당 없음        |
| ML 파이프라인과 Azure Databricks 통합                             | GA               | 아니요        | 해당 없음        |
| Azure ML 디자이너에서 파이프라인 엔드포인트 만들기                             | GA   | YES       | 해당 없음        |
| **통합 Notebook** |   | | |
| 작업 영역 Notebook 및 파일 공유                                        | GA               | YES       | 해당 없음        |
| R 및 Python 지원                                                       | GA               | YES       | 해당 없음        |
| Virtual Network 지원                                                    | 공개 미리 보기   | 아니요        | 해당 없음        |
| **컴퓨팅 인스턴스** |    | | |
| 통합 Notebook에 대한 관리되는 컴퓨팅 인스턴스                         | GA               | YES        | 해당 없음        |
| Jupyter, JupyterLab 통합                                            | GA               | YES       | 해당 없음        |
| 가상 네트워크(VNet) 지원                                             | 공개 미리 보기   | YES       | 해당 없음        |
| **SDK 지원** |    | | |
| Python SDK 지원                                                         | GA               | YES       | 해당 없음        |
| **보안** |   | | |
| 학습을 위한 VNet(Virtual Network) 지원                                | GA               | YES       | 해당 없음        |
| 유추를 위한 VNet(Virtual Network) 지원                               | GA               | YES       | 해당 없음        |
| 엔드포인트 인증 점수 매기기                                            | 공개 미리 보기   | YES       | 해당 없음        |
| 작업 영역 프라이빗 엔드포인트                                                 | GA               | 아니요        | 해당 없음        |
| VNet 뒤에 있는 ACI                                                            | 공개 미리 보기   | 아니요        | 해당 없음        |
| VNet 뒤에 있는 ACR                                                            | GA   | YES       | 해당 없음        |
| AKS 클러스터의 개인 IP                                                  | 공개 미리 보기   | 아니요        | 해당 없음        |
| **컴퓨팅** |   | | |
| 작업 영역에서 할당량 관리                                         | GA               | YES       | 해당 없음        |
| **기계 학습에 대한 데이터** | | | |
| SDK에서 데이터 세트 및 데이터 저장소 만들기, 보기 또는 편집                  | GA               | YES       | 해당 없음        |
| UI에서 데이터 세트 및 데이터 저장소 만들기, 보기 또는 편집                   | GA               | YES       | 해당 없음        |
| SDK에서 데이터 세트 드리프트 모니터 보기, 편집 또는 삭제                   | 공개 미리 보기   | YES       | 해당 없음        |
| UI에서 데이터 세트 드리프트 모니터 보기, 편집 또는 삭제                    | 공개 미리 보기   | YES       | 해당 없음        |
| **기계 학습 수명 주기** |    | | |
| 모델 프로파일링                                                            | GA               | PARTIAL   | 해당 없음        |
| Machine Learning 및 Azure ML CLI에 대한 Azure DevOps 확장         | GA               | YES       | 해당 없음        |
| FPGA 기반 하드웨어 가속 모델                                     | GA               | 아니요        | 해당 없음        |
| Visual Studio Code 통합                                             | 공개 미리 보기   | 아니요        | 해당 없음        |
| Event Grid 통합                                                     | 공개 미리 보기   | YES       | 해당 없음        |
| Azure Machine Learning과 Azure Stream Analytics 통합               | 공개 미리 보기   | 아니요        | 해당 없음        |
| **레이블 지정** |    | | |
| 프로젝트 관리 포털 레이블 지정                                        | GA               | YES       | 해당 없음        |
| 레이블 지정자 포털                                                            | GA               | YES       | 해당 없음        |
| 개인 인력을 사용하여 레이블 지정                                          | GA               | YES       | 해당 없음        |
| ML 지원 레이블 지정(이미지 분류 및 개체 감지)           | 공개 미리 보기   | YES       | 해당 없음        |
| **Responsible ML** |    | | |
| UI의 설명                                                       | 공개 미리 보기   | 아니요        | 해당 없음        |
| 차등 개인 정보 SmartNoise 도구 키트                                    | OSS              | 아니요        | 해당 없음        |
| 데이터 시트를 구현하는 Azure Machine Learning의 사용자 지정 태그              | GA               | 아니요        | 해당 없음        |
| 공정성 AzureML 통합                                               | 공개 미리 보기   | 아니요        | 해당 없음        |
| 해석력 SDK                                                      | GA               | YES       | 해당 없음        |
| **학습** |    | | |
| 실험 로그 스트리밍                                              | GA               | YES       | 해당 없음        |
| 보충 학습                                                     | 공개 미리 보기   | 아니요        | 해당 없음        |
| 실험 UI                                                         | GA               | YES       | 해당 없음        |
| .NET 통합 ML.NET 1.0                                                | GA               | YES       | 해당 없음        |
| **유추** |   | | |
| 일괄 처리 추론                                                          | GA               | YES       | 해당 없음        |
| FPGA를 사용하여 Data Box Edge                                                    | 공개 미리 보기   | 아니요        | 해당 없음        |
| **기타** |    | | |
| Open Datasets                                                              | 공개 미리 보기   | YES       | 해당 없음        |
| 사용자 지정 Cognitive Search                                                    | 공개 미리 보기   | YES       | 해당 없음        |



### <a name="other-azure-china-limitations"></a>기타 Azure 중국 제한 사항

* Azure 중국에는 특히 GPU SKU에 대한 VM SKU가 제한되어 있습니다. NCv3 제품군(V100)만 있습니다.
* REST API 엔드포인트는 글로벌 Azure와 다릅니다. 다음 표를 사용하여 Azure 중국 지역에 대한 REST API 엔드포인트를 찾습니다.

    | REST 엔드포인트                 | 글로벌 Azure                                 | 중국 정부                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | 관리 평면 | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | 데이터 평면       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* 공용 데이터에 액세스해야 하는 경우 샘플 Notebook이 작동하지 않을 수 있습니다.
* IP 주소 범위: [필수 공용 인터넷 액세스](how-to-secure-training-vnet.md#required-public-internet-access) 지침에 사용된 CLI 명령은 IP 범위를 반환하지 않습니다. 대신 [Azure 중국에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)를 사용합니다.
* 현재는 프라이빗 엔드포인트가 사용하도록 설정된 작업 영역에서는 Azure Machine Learning 컴퓨팅 인스턴스 미리 보기가 지원되지 않지만, CI는 모든 AML 지역으로의 서비스 확장을 위한 다음 배포에서 지원됩니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning을 사용할 수 있는 지역에 대한 자세한 내용은 [지역별 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.