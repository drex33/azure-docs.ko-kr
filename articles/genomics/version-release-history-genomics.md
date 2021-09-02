---
title: 버전 릴리스 기록
titleSuffix: Microsoft Genomics
description: 수정 사항 및 새로운 기능을 위한 Microsoft Genomics Python 클라이언트에 대한 업데이트의 릴리스 기록입니다.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 3023f01b9a9ccbc3a56c509b1edc86885f2af41e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255695"
---
# <a name="version-release-history"></a>버전 릴리스 기록
Microsoft Genomics 팀은 수정 사항 및 새로운 기능으로 Microsoft Genomics Python 클라이언트를 정기적으로 업데이트합니다. 

## <a name="latest-release"></a>최신 릴리스
최신 Python 클라이언트인 버전 0.9.0은 2019년 2월 6일 릴리스되었으며 GATK 3.5 및 GATK4를 통해 실행 중인 워크플로를 지원합니다. gVCF 출력을 지원하며 출력 압축에 대한 선택적 인수를 수락할 수 있습니다.


## <a name="release-history"></a>릴리스 기록 
새 버전의 Microsoft Genomics Python 클라이언트는 1년에 한 번 정도 릴리스됩니다. 새 버전의 Microsoft Genomics Python 클라이언트가 릴리스되면 여기에서 수정 사항 및 기능 목록이 업데이트됩니다. 새 버전이 릴리스되어도 이전 버전은 적어도 90일 동안 계속 지원됩니다. 이전 버전이 더 이상 지원되지 않으면 이 페이지에 표시됩니다. 

### <a name="version-090"></a>버전 0.9.0
버전 0.9.0에는 출력 압축에 대한 지원이 포함됩니다. 이는 vcf 또는 gvcf 출력에서 `-bgzip`을 실행하고 `-tabix`를 실행하는 것과 같습니다. 자세한 내용은 [질문과 대답](frequently-asked-questions-genomics.yml)을 참조하세요. 

### <a name="version-081"></a>버전 0.8.1
버전 0.8.1에는 사소한 버그 수정 사항이 포함되어 있습니다.  

### <a name="version-080"></a>버전 0.8.0
버전 0.8.0에는 GATK4 및 gVCFs 출력 지원이 포함되어 있습니다.  

### <a name="version-074"></a>버전 0.7.4
버전 0.7.4에서는 `config.txt` 입력에서 계정 키 대신 SAS 토큰을 수락하도록 지원합니다. 자세한 내용은 [입력 SAS 토큰 빠른 시작](quickstart-input-sas.md)을 참조하세요. 

### <a name="version-073"></a>버전 0.7.3
버전 0.7.3에는 사소한 버그 수정 사항이 포함되어 있습니다.

### <a name="version-072"></a>버전 0.7.2
버전 0.7.2는 초기 버전입니다. 이 버전은 2017년 11월 1일에 릴리스되었습니다.
