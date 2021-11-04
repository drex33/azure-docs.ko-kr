---
title: 온-프레미스에서 Azure Cognitive Services 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: Docker 컨테이너를 사용하여 온-프레미스에서 Cognitive Services를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.service: cognitive-services
ms.topic: article
ms.date: 10/28/2021
ms.author: aahi
keywords: 온-프레미스, Docker, 컨테이너, Kubernetes
ms.openlocfilehash: 0b81d190aa7b565f40dd73fa5054253a46ae7041
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439256"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services 컨테이너

Azure Cognitive Services는 Azure, 온-프레미스에서 사용할 수 있는 것과 동일한 API를 사용할 수 있는 여러 [Docker 컨테이너](https://www.docker.com/what-container)를 제공합니다. 이러한 컨테이너를 사용하면 규정 준수, 보안 또는 기타 운영상의 이유로 Cognitive Services를 데이터에 더 가깝게 가져올 수 있는 유연성이 제공됩니다. 컨테이너 지원은 현재 Azure Cognitive Services 하위 집합에 사용할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

컨테이너화는 종속성 및 구성을 포함한 애플리케이션 또는 서비스가 컨테이너 이미지로 패키징되는 소프트웨어 배포 방법입니다. 컨테이너 이미지를 거의 또는 전혀 수정하지 않고 컨테이너 호스트에 배포할 수 있습니다. 컨테이너는 서로 그리고 기본 운영 체제와 격리되며, 가상 머신보다 공간을 적게 차지합니다. 단기 작업용 컨테이너 이미지에서 컨테이너화를 시작하고, 더 이상 필요 없으면 제거할 수 있습니다.

## <a name="features-and-benefits"></a>기능 및 혜택

- **변경할 수 없는 인프라**: DevOps 팀이 일관되고 신뢰할 수 있는 알려진 시스템 매개 변수 집합을 활용하면서 변경에 적응할 수 있도록 합니다. 컨테이너는 예측 가능한 에코시스템 내에서 피벗하고 구성 드리프트를 방지할 수 있는 유연성을 제공합니다.
- **데이터 컨트롤**: Cognitive Services가 데이터를 처리하는 위치를 선택합니다. 사용자가 클라우드로 데이터를 보낼 수 없지만 Cognitive Services API에 액세스해야 하는 경우에 중요할 수 있습니다. 하이브리드 환경의 데이터, 관리, ID 및 보안에 걸쳐 일관성을 지원합니다.
- **모델 업데이트 컨트롤**: 사용자가 솔루션에 배포된 모델의 버전 관리 및 업데이트를 유연하게 처리할 수 있습니다.
- **이식 가능한 아키텍처**: Azure, 온-프레미스 및 에지에 배포할 수 있는 이식 가능한 애플리케이션 아키텍처를 만들 수 있습니다. 컨테이너는 [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml) 또는 [Azure Stack](/azure-stack/operator)에 배포된 [Kubernetes](https://kubernetes.io/) 클러스터에 직접 배포할 수 있습니다. 자세한 내용은 [Azure Stack에 Kubernetes 배포](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)를 참조하세요.
- **높은 처리량/짧은 대기 시간**: Cognitive Services가 애플리케이션 로직 및 데이터와 물리적으로 가까운 거리에서 실행되게 만들어서 높은 처리량과 짧은 대기 시간이라는 요구 사항에 맞게 크기를 조정하는 기능을 고객에게 제공합니다. 컨테이너는 TPS(초당 트랜잭션 수)를 제한하지 않으며, 고객이 필요한 하드웨어 리소스를 제공한다면 스케일업 및 스케일아웃이 가능합니다.
- **확장성**: 컨테이너화 및 컨테이너 오케스트레이션 소프트웨어(예: Kubernetes)의 인기가 계속 증가함에 따라 확장성은 기술 발전을 선도하고 있습니다. 확장성 있는 클러스터 기반을 바탕으로 하는 애플리케이션 개발은 고가용성에 부합합니다.

## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services의 컨테이너

Azure Cognitive Services 컨테이너는 다음과 같은 Docker 컨테이너 집합을 제공하며, 각각은 Azure Cognitive Services 서비스의 기능 하위 집합을 포함하고 있습니다. 아래 표에서 지침 및 이미지 위치를 찾을 수 있습니다. [컨테이너 이미지](containers/container-image-tags.md) 목록도 확인할 수 있습니다.

### <a name="decision-containers"></a>의사 결정 컨테이너

| 서비스 |  컨테이너 | Description | 가용성 |
|--|--|--|--|
| [Anomaly detector][ad-containers] | **Anomaly Detector** ([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Anomaly Detector API를 사용하면 기계 학습을 통해 시계열 데이터에서 변칙을 검색하고 모니터링할 수 있습니다. | 일반 공급 |

### <a name="language-containers"></a>언어 컨테이너

| 서비스 |  컨테이너 | Description | 가용성 |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS**([이미지](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | LUIS 앱으로 알려진 학습된 또는 게시된 Language Understanding 모델을 Docker 컨테이너로 로드하고, 컨테이너의 API 엔드포인트에서 쿼리 예측에 대한 액세스를 제공합니다. 컨테이너에서 쿼리 로그를 수집하고 [LUIS 포털](https://www.luis.ai)에 다시 업로드하여 앱의 예측 정확도를 개선할 수 있습니다. | 일반 공급 |
| [언어 서비스][ta-containers-keyphrase] | **핵심 구 추출**([이미지](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | 핵심 구를 추출하여 주요 요소를 식별합니다. 예를 들어 "The food was delicious and there were wonderful staff"라는 입력 텍스트에 대해 이 API는 "food" 및 "wonderful staff"이라는 주요 논점을 반환합니다. | 미리 보기 |
| [언어 서비스][ta-containers-language] |  **텍스트 언어 감지**([이미지](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | 최대 120개 언어에 대해, 입력 텍스트를 쓴 언어를 감지하고 요청에 따라 제출된 모든 문서에 대해 단일 언어 코드를 보고합니다. 언어 코드가 점수와 쌍을 이루어 점수의 강도를 나타냅니다. | 일반 공급 |
| [언어 서비스][ta-containers-sentiment] | **감정 분석**([이미지](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | 원시 텍스트를 분석하여 긍정 또는 부정적인 감정에 대한 단서를 찾습니다. 이 버전의 감정 분석은 각 문서 및 문서 속 문장에 대한 감정 레이블(예: *긍정* 또는 *부정*)을 반환합니다. |  일반 공급 |
| [언어 서비스][ta-containers-health] |  **의료 분야 Text Analytics** | 비구조적 임상 텍스트에서 의료 정보를 추출하고 레이블을 지정합니다. | 미리 보기 |
| [Translator][tr-containers] | **Translator** | 텍스트를 여러 언어 및 방언으로 번역합니다. | 게이트를 미리 봅니다. [액세스를 요청합니다](https://aka.ms/csgate-translator). | 

### <a name="speech-containers"></a>음성 컨테이너

> [!NOTE]
> 음성 컨테이너를 사용하려면 [온라인 요청 양식](https://aka.ms/csgate)을 완료해야 합니다.

| 서비스 |  컨테이너 | Description | 가용성 |
|--|--|--|
| [Speech Service API][sp-containers-stt] |  **음성 텍스트 변환**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | 연속적인 실시간 음성을 텍스트로 변환합니다. | 일반 공급 |
| [Speech Service API][sp-containers-cstt] | **사용자 지정 음성 텍스트 변환**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | 사용자 지정 모델을 사용하여 실시간으로 음성 텍스트를 연속 변환합니다. | 일반 공급 |
| [Speech Service API][sp-containers-tts] | **텍스트 음성 변환**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | 텍스트를 자연스럽게 들리는 음성으로 변환합니다. | 일반 공급 |
| [Speech Service API][sp-containers-ctts] | **사용자 지정 텍스트 음성 변환**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | 사용자 지정 모델을 사용하여 텍스트를 자연스러운 음성으로 변환합니다. | 게이트 미리 보기 |
| [Speech Service API][sp-containers-ntts] | **인공신경망 텍스트 음성 변환**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | 심층 신경망 기술을 사용하여 텍스트를 자연스러운 음성으로 변환함으로써 보다 자연스러운 합성 음성을 사용할 수 있도록 합니다. | 일반 공급 |
| [Speech Service API][sp-containers-lid] | **음성 언어 검색**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | 음성 오디오의 언어를 결정합니다. | 게이트 미리 보기 |

### <a name="vision-containers"></a>비전 컨테이너

> [!WARNING]
> 2020년 6월 11일, Microsoft는 인권에 기반한 강력한 규정이 적용될 때까지 미국 경찰 당국에 얼굴 인식 기술을 판매하지 않겠다고 발표했습니다. 따라서 미국 경찰 당국에 의해 또는 미국 경찰 당국을 위해 이 서비스를 사용하거나 허용하는 경우 고객은 얼굴 인식 기능 또는 Face나 Video Indexer와 같은 Azure 서비스에 포함된 기능을 사용하지 않을 수도 있습니다.

| 서비스 |  컨테이너 | Description | 가용성 |
|--|--|--|--|
| [Computer Vision][cv-containers] | **Read OCR**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Read OCR 컨테이너를 사용하면 JPEG, PNG, BMP, PDF 및 TIFF 파일 형식에 대한 지원이 포함된 이미지와 문서로부터 인쇄된 텍스트 및 필기 텍스트를 추출할 수 있습니다. 자세한 내용은 [Read API 설명서](./computer-vision/overview-ocr.md)를 참조하세요. | 게이트를 미리 봅니다. [액세스를 요청합니다][request-access]. |
| [공간 분석][spa-containers] | **공간 분석**([이미지](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | 실시간 스트리밍 비디오를 분석하여 사람 사이의 공간적 관계, 이들의 움직임 및 물리적 환경에서 물체와의 상호 작용을 이해할 수 있습니다. | 미리 보기 |
| [Face][fa-containers] | **Face** | 이미지에서 사람의 얼굴을 감지하고, 얼굴 랜드마크(예: 코, 눈), 성별, 연령, 기타 머신 예측 얼굴 특징 등을 포함한 특성을 식별합니다. 감지 외에도 신뢰도 점수를 사용하여 동일하거나 다른 이미지의 두 얼굴이 동일한지 확인하거나, 얼굴을 데이터베이스와 비교하여 비슷하거나 동일한 얼굴이 이미 있는지 확인합니다. 공유된 시각적 특성을 사용하여 비슷한 얼굴을 그룹으로 구성할 수도 있습니다. | 사용할 수 없음 |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

또한 일부 컨테이너는 Cognitive Services [다중 서비스 리소스](cognitive-services-apis-create-account.md) 제공에서 지원됩니다. 하나의 단일 Cognitive Services 올인원 리소스를 만들고 다음 서비스에 대해 지원되는 서비스 전반에서 동일한 청구 키를 사용할 수 있습니다.

* Computer Vision
* Face
* LUIS
* 언어 서비스

## <a name="prerequisites"></a>전제 조건

Azure Cognitive Services 컨테이너를 사용하려면 다음 필수 조건을 충족해야 합니다.

**Docker 엔진**: Docker 엔진이 로컬로 설치되어 있어야 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 및 [Windows](https://docs.docker.com/docker-for-windows/)에 Docker 환경을 구성하는 패키지를 제공합니다. Windows에서 Docker는 Linux 컨테이너를 지원하도록 구성해야 합니다. Docker 컨테이너는 [Azure Kubernetes Service](../aks/index.yml) 또는 [Azure Container Instances](../container-instances/index.yml)에 직접 배포할 수도 있습니다.

Docker는 컨테이너에서 Azure에 연결하여 청구 데이터를 보낼 수 있도록 구성해야 합니다.

**Microsoft Container Registry 및 Docker에 대한 숙지**: 기본적으로 레지스트리, 리포지토리, 컨테이너 및 컨테이너 이미지는 물론 기본 `docker` 명령에 대한 지식과 같이 Microsoft Container Registry와 Docker 모두에 대한 개념을 이해해야 합니다.

Docker 및 컨테이너에 대한 기본 사항은 [Docker 개요](https://docs.docker.com/engine/docker-overview/)를 참조하세요.

개별 컨테이너마다 서버 및 메모리 할당 요구 사항을 포함하여 각각 고유한 요구 사항이 있을 수 있습니다.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>개발자 샘플

개발자 샘플은 [GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-containers-samples)에 있습니다.

## <a name="next-steps"></a>다음 단계

Cognitive Services에서 사용할 수 있는 [컨테이너 레시피](containers/container-reuse-recipe.md)에 대해 알아봅니다.

Azure Cognitive Services의 컨테이너에서 제공하는 기능을 설치하고 탐색합니다.

* [Anomaly Detector 컨테이너][ad-containers]
* [Computer Vision 컨테이너][cv-containers]
* [Face 컨테이너][fa-containers]
* [LUIS(Language Understanding) 컨테이너][lu-containers]
* [Speech Service API 컨테이너][sp-containers]
* [언어 서비스 컨테이너][ta-containers]
* [Translator 컨테이너][tr-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/overview.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: language-service/overview.md#deploy-on-premises-using-docker-containers
[ta-containers-keyphrase]: language-service/key-phrase-extraction/how-to/use-containers.md
[ta-containers-language]: language-service/language-detection/how-to/use-containers.md
[ta-containers-sentiment]: language-service/sentiment-opinion-mining/how-to/use-containers.md
[ta-containers-health]: language-service/text-analytics-for-health/how-to/use-containers.md
[tr-containers]: translator/containers/translator-how-to-install-container.md
[request-access]: https://aka.ms/csgate
