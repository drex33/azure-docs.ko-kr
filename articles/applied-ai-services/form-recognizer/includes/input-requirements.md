---
author: laujan
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: fbf4fc405c92700f993a488d94334338ab2757b4
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122327228"
---
Form Recognizer는 다음 요구 사항을 충족하는 입력 문서에서 작동합니다.

* 형식은 JPG, PNG, PDF(텍스트 또는 스캔) 또는 TIFF여야 합니다. 문자 추출 및 위치에 오류가 발생할 가능성이 없으므로 텍스트가 포함된 PDF가 가장 좋습니다.
* 파일 크기는 50MB 미만이어야 합니다.
* 이미지 크기는 50x50 픽셀에서 10,000x10,000 픽셀 사이여야 합니다.
* PDF 크기는 최대 17x17인치(Legal 또는 A3 용지 크기 이하에 해당)여야 합니다.
* PDF 및 TIFF의 경우 처음 200페이지만 처리됩니다(체험 계층 구독의 경우 처음 2페이지만 처리됨).
* 학습 데이터 세트의 총 크기는 500페이지 이하여야 합니다.
* PDF가 암호로 잠긴 경우에는 제출하기 전에 잠금을 제거해야 합니다.
* 종이 문서에서 스캔한 경우 양식은 고품질 스캔이어야 합니다.
* 자율 학습(레이블이 지정된 데이터 제외)의 경우 데이터에는 키와 값이 포함되어야 합니다.
* 자율 학습(레이블이 지정된 데이터 제외)의 경우 키가 값의 위 또는 왼쪽에 표시되어야 합니다. 이는 아래 또는 오른쪽에 표시되지 않습니다.
