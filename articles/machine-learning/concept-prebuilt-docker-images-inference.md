---
title: 미리 빌드된 Docker 이미지
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 유추(채점)를 위해 미리 빌드된 Docker 이미지
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 0f7fc034ee6df962ecfe6e00b62b8899e223505c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563704"
---
# <a name="prebuilt-docker-images-for-inference"></a>유추를 위한 미리 빌드된 Docker 이미지

Azure Machine Learning를 사용 하 여 모델을 배포 하는 경우 유추를 위한 미리 빌드된 Docker 컨테이너 이미지가 사용 됩니다.  이미지는 널리 사용되는 기계 학습 프레임워크 및 Python 패키지로 미리 빌드됩니다. 다음 방법 중 하나를 사용해 패키지를 확장하여 다른 패키지를 추가할 수도 있습니다.

* [Python 패키지를 추가](how-to-prebuilt-docker-images-inference-python-extensibility.md)합니다.
* [미리 빌드된 유추 이미지를 새 Dockerfile의 기반으로 사용](how-to-extend-prebuilt-docker-image-inference.md)합니다. 이 방법을 사용하면 **Python 패키지와 apt 패키지** 를 모두 설치할 수 있습니다.

## <a name="why-should-i-use-prebuilt-images"></a>미리 빌드된 이미지를 사용해야 하는 이유는 무엇인가요?

* 모델 배포 대기 시간을 줄입니다.
* 모델 배포 성공률을 향상시킵니다.
* 모델을 배포하는 동안 불필요한 이미지 빌드를 방지합니다.
* 이미지/컨테이너에 필요한 종속성 및 액세스 권한만 있어야 합니다. 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>유추를 위해 미리 빌드된 Docker 이미지 목록 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>다음 단계

* [Python 패키지를 미리 빌드된 이미지에 추가](how-to-prebuilt-docker-images-inference-python-extensibility.md)합니다.
* [미리 빌드된 패키지를 새 Dockerfile의 기반으로 사용](how-to-extend-prebuilt-docker-image-inference.md)합니다.