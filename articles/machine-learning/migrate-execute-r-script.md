---
title: 'ML 스튜디오(클래식): R 스크립트 실행 마이그레이션'
description: Studio(클래식)를 다시 빌드하여 Azure Machine Learning에서 실행할 R 스크립트 모듈을 실행합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 7d8f014b752d4ff0838195671ef8c919a164ebef
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699326"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Studio(클래식)에서 R 스크립트 실행 모듈 마이그레이션

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

이 문서에서는 Azure Machine Learning에서 Studio(클래식) **R 스크립트 실행** 모듈을 다시 빌드하는 방법에 대해 알아봅니다.

Studio(클래식)에서 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조하세요.

## <a name="execute-r-script"></a>R 스크립트 실행

Azure Machine Learning 디자이너는 이제 Linux에서 실행됩니다. Studio(클래식)는 Windows에서 실행됩니다. 플랫폼 변경으로 인해 마이그레이션 중에 **R 스크립트 실행** 을 조정해야 합니다. 그렇지 않으면 파이프라인이 실패합니다.

Studio(클래식)에서 **R 스크립트 실행** 모듈을 마이그레이션하려면 `maml.mapInputPort` 및 `maml.mapOutputPort` 인터페이스를 표준 함수로 바꾸어야 합니다.

다음 표에는 R 스크립트 모듈의 변경 내용이 요약되어 있습니다.

|기능|Studio(클래식)|Azure Machine Learning 디자이너|
|---|---|---|
|스크립트 인터페이스|`maml.mapInputPort` 및 `maml.mapOutputPort`|함수 인터페이스|
|플랫폼|Windows|Linux|
|인터넷 액세스 가능 |예|예|
|메모리|14 GB|컴퓨팅 SKU에 종속|

### <a name="how-to-update-the-r-script-interface"></a>R 스크립트 인터페이스를 업데이트하는 방법

다음은 Studio(클래식)에 있는 샘플 **R 스크립트 실행** 모듈의 콘텐츠입니다.
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

디자이너에서 업데이트된 내용은 다음과 같습니다. `maml.mapInputPort` 및 `maml.mapOutputPort`가 표준 함수 인터페이스 `azureml_main`으로 대체되었습니다. 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
자세한 내용은 디자이너 [R 스크립트 실행 모듈 참조](/algorithm-module-reference/execute-r-script.md)를 참조하세요.

### <a name="install-r-packages-from-the-internet"></a>인터넷에서 새 R 패키지 설치

Azure Machine Learning 디자이너를 사용하면 CRAN에서 직접 패키지를 설치할 수 있습니다.

이는 Studio(클래식)보다 향상된 것입니다. Studio(클래식)는 인터넷에 액세스할 수 없는 샌드박스 환경에서 실행되므로 추가 패키지를 설치하려면 zip 번들로 스크립트를 업로드해야 했습니다. 

다음 코드를 사용하여 디자이너의 **R 스크립트 실행** 모듈에 CRAN 패키지를 설치합니다.
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 R 스크립트 실행 모듈을 Azure Machine Learning으로 마이그레이션하는 방법에 대해 알아보았습니다.

Studio(클래식) 마이그레이션 시리즈의 다른 문서를 참조하세요.

1. [마이그레이션 개요](migrate-overview.md)입니다.
1. [마이그레이션 데이터세트](migrate-register-dataset.md)
1. [Studio(클래식) 학습 파이프라인을 다시 빌드](migrate-rebuild-experiment.md)합니다.
1. [Studio(클래식) 웹 서비스를 다시 빌드](migrate-rebuild-web-service.md)합니다.
1. [Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. **R 스크립트 실행 모듈을 마이그레이션합니다**.