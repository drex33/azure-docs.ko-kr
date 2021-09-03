---
title: Azure Container Instances에서 Anomaly Detector 컨테이너 실행
titleSuffix: Azure Cognitive Services
description: Anomaly Detector 컨테이너를 Azure Container Instance에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: ec057d625342c2e80478b0555395f6bc5250ee6b
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296778"
---
# <a name="deploy-an-anomaly-detector-univariate-container-to-azure-container-instances"></a>Azure Container Instances에 Anomaly Detector 단변량 컨테이너 배포

Cognitive Services [Anomaly Detector](../anomaly-detector-container-howto.md) 컨테이너를 Azure [Container Instances](../../../container-instances/index.yml)에 배포하는 방법을 알아봅니다. 이 절차에서는 Anomaly Detector 리소스를 만드는 방법을 보여 줍니다. 그런 다음 연결된 컨테이너 이미지를 풀하는 방법을 설명합니다. 마지막으로 브라우저에서 이 둘의 오케스트레이션을 실행하는 기능을 강조합니다. 컨테이너를 사용하면 개발자가 인프라 관리보다 애플리케이션 개발에 집중할 수 있습니다.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행](../anomaly-detector-container-configuration.md)을 검토하여 컨테이너 이미지를 풀하고 컨테이너를 실행합니다.
* [컨테이너 구성](../anomaly-detector-container-configuration.md)에서 구성 설정을 검토합니다.
* [Anomaly Detector API 서비스에 대해 자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)