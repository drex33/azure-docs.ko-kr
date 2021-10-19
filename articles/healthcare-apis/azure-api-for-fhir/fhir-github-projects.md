---
title: Azure API for FHIR 관련 GitHub 프로젝트
description: Azure API for FHIR 대한 모든 GitHub(오픈 소스) 리포지토리를 나열합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436193"
---
# <a name="related-github-projects"></a>관련 GitHub 프로젝트

다양한 용도로 서비스를 배포하기 위한 소스 코드와 지침을 제공하는 GitHub 오픈 소스 프로젝트가 많이 있습니다. 항상 GitHub 리포지토리를 방문하여 기능 및 제품을 알아보고 실험해 볼 수 있습니다. 

## <a name="fhir-server"></a>FHIR 서버
* [microsoft/fhir-server:](https://github.com/microsoft/fhir-server/)오픈 소스 FHIR 서버로, Azure API for FHIR
* 최신 릴리스를 보려면 [릴리스](https://github.com/microsoft/fhir-server/releases) 정보
* [microsoft/fhir-server-samples:](https://github.com/microsoft/fhir-server-samples)샘플 환경

## <a name="data-conversion--anonymization"></a>데이터 변환 & 익명화

#### <a name="fhir-converter"></a>FHIR 변환기
* [microsoft/FHIR-Converter:](https://github.com/microsoft/FHIR-Converter)레거시 데이터 형식을 FHIR로 변환하는 변환 유틸리티입니다.
* Azure API for FHIR 및 Azure용 FHIR 서버와 $convert-data 작업 형식으로 통합
* OSS의 지속적인 개선 및 FHIR 서버에 대한 지속적인 통합
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR 변환기 - VS Code 확장
* [microsoft/FHIR-Tools-for-Anonymization:](https://github.com/microsoft/FHIR-Tools-for-Anonymization)데이터(FHIR 형식) 익명화를 돕는 도구 집합입니다.
* '식별되지 않은 내보내기' 형식으로 Azure용 FHIR 서버뿐만 아니라 Azure API for FHIR 통합

#### <a name="fhir-tools-for-anonymization"></a>익명화를 위한 FHIR 도구
* [microsoft/vscode-azurehealthcareapis-tools:](https://github.com/microsoft/vscode-azurehealthcareapis-tools)Azure Healthcare API를 사용하는 도구 모음이 포함된 VS Code 확장입니다.
* Visual Studio Marketplace에 릴리스됨
* FHIR 변환기에서 사용할 Liquid 템플릿을 작성하는 데 사용됩니다.

## <a name="iot-connector"></a>IoT 커넥터

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub 및 IoT Central 통합
* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)정규화된 데이터의 데이터 정규화 및 FHIR 변환을 사용하여 FHIR에 IoT Hub 또는 IoT Central 통합
* 정규화: 추가 처리를 위해 디바이스 데이터 정보가 일반적인 형식으로 추출됩니다.
* FHIR 변환: 정규화된 데이터와 그룹화된 데이터가 FHIR에 매핑됩니다. 관찰은 구성된 템플릿에 따라 생성되거나 업데이트되며 디바이스 및 환자와 연결됩니다.
* [대화 맵을 빌드하는 데 도움이 되는 도구:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)디바이스 입력 데이터를 정규화하기 위한 매핑 구성을 시각화하고 FHIR 리소스로 변환합니다. 개발자는 이 도구를 사용하여 매핑, 디바이스 매핑 및 FHIR 매핑을 편집 및 테스트하고 Azure Portal IoT Connector 업로드하기 위해 내보낼 수 있습니다.

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 및 FHIR 통합
* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Apple HealthKit 데이터를 FHIR 서버로 내보내는 것을 자동화하는 Swift 라이브러리입니다.

 