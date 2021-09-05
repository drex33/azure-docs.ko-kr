---
title: Azure 기반 PyTorch Enterprise에 대한 경계 지원
description: 이 문서에서는 PyTorch Enterprise에 대한 경계 지원을 정의합니다.
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598702"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>PyTorch Enterprise에 대한 경계 지원

이 간단한 문서에서는 Pytorch Enterprise에서 지원되는 모듈 및 구성 요소에 대해 설명합니다.


|영역|지원되는 버전|참고|
|----|----|----|
|OS|Windows 10, Debian 9, Debian 10, Ubuntu 16.04.7 LTS, Ubuntu 18.04.5 LTS|Debian 및 Ubuntu 배포판의 LTS 버전과 x86_64 아키텍처만 지원합니다.|
|Python|3.6 이상||
|PyTorch|1.8.1+||
|CUDA Toolkit|10.2, 11.1||
|ONNX Runtime|1.7+||
|torchtext, torchvision, torch-tb-profiler, torchaudio| - |1\.0 릴리스가 없는 라이브러리의 경우 지원되는 해당 PyTorch 버전과 호환되는 특정 버전을 지원합니다. 예를 들어 [TorchVision](https://github.com/pytorch/vision#installation), [TorchText](https://github.com/pytorch/text#installation), [TorchAudio](https://github.com/pytorch/audio/#dependencies) 테이블을 참조하세요.|
|torchserve|0.4.0+||
