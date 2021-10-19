---
title: Azure 의료 api에 대 한 관련 GitHub 프로젝트
description: 모든 오픈 소스 (GitHub) 리포지토리 나열
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: ginle
ms.openlocfilehash: 63e3ff442ab45801d079e4f61b8dae98a9b010f4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166370"
---
# <a name="github-projects"></a>GitHub 프로젝트

다양 한 용도로 서비스를 배포 하는 데 필요한 소스 코드와 지침을 제공 하는 GitHub에 여러 오픈 소스 프로젝트가 있습니다. 항상 GitHub 리포지토리를 방문 하 여 기능 및 제품을 배우고 시험해 볼 수 있습니다. 

## <a name="healthcare-apis-samples"></a>의료 Api 샘플

* 이 리포지토리에는 FHIR&#174;), DICOM, IoT 커넥터 및 데이터 관련 서비스를 비롯 하 여 [의료 api에 대 한 샘플이](https://github.com/microsoft/healthcare-apis-samples)포함 되어 있습니다.

## <a name="fhir-server"></a>서버

* [microsoft/fhir-서버](https://github.com/microsoft/fhir-server/): fhir 서비스의 기초가 되는 오픈 소스 Fhir 서버
* 최신 릴리스에 대 한 자세한 내용은 [릴리스 정보](https://github.com/microsoft/fhir-server/releases) 를 참조 하세요.
* [microsoft/fhir-서버-샘플](https://github.com/microsoft/fhir-server-samples): 샘플 환경

## <a name="data-conversion--anonymization"></a>데이터 변환 & 익명화

#### <a name="fhir-converter"></a>FHIR 변환기

* [microsoft/fhir-변환기](https://github.com/microsoft/FHIR-Converter): 레거시 데이터 형식을 FHIR로 변환 하는 변환 유틸리티
* FHIR 서비스 및 Azure 용 FHIR 서버를 $convert 데이터 작업 형식으로 통합
* OSS의 지속적인 개선 및 FHIR 서버에 지속적으로 통합
 
#### <a name="fhir-converter---vs-code-extension"></a>fhir 변환기-VS Code 확장

* [microsoft/익명화](https://github.com/microsoft/FHIR-Tools-for-Anonymization): 데이터를 지 원하는 데 사용 되는 도구 집합 (fhir 형식) 익명화
* FHIR 서비스 및 Azure 용 FHIR 서버를 ' 확인 되지 않은 내보내기 ' 형식으로 통합

#### <a name="fhir-tools-for-anonymization"></a>익명화 용 FHIR 도구

* [microsoft/vscode](https://github.com/microsoft/vscode-azurehealthcareapis-tools): Azure 의료 api를 사용 하는 도구 컬렉션이 포함 된 VS Code 확장
* Visual Studio Marketplace에 릴리스 됨
* FHIR 변환기에서 사용할 액체 템플릿을 제작 하는 데 사용 됩니다.

## <a name="iot-connector"></a>IoT 커넥터

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub 및 IoT Central와 통합

* [microsoft/iomt r](https://github.com/microsoft/iomt-fhir): 정규화 된 데이터의 데이터 정규화 및 fhir 변환으로 IoT Hub 또는 IoT Central와의 통합
* 정규화: 추가 처리를 위해 장치 데이터 정보가 공통 형식으로 추출 됩니다.
* FHIR 변환: 정규화 된 데이터와 그룹화 된 데이터는 FHIR에 매핑됩니다. 관찰은 구성 된 템플릿에 따라 만들어지거나 업데이트 되며 장치 및 환자에 연결 됩니다.
* [대화 지도를 작성 하는 데 도움이](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)되는 도구: 장치 입력 데이터를 정규화 하 고 FHIR 리소스로 변환 하기 위한 매핑 구성을 시각화 합니다. 개발자는이 도구를 사용 하 여 장치 및 FHIR 대상 매핑을 편집 및 테스트 하 고 Azure Portal의 IoT 커넥터에 업로드 하기 위해 내보낼 수 있습니다.

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 및 FHIR 통합

* [microsoft/healthkit r](https://github.com/microsoft/healthkit-on-fhir): Fhir 서버에 Apple healthkit 데이터 내보내기를 자동화 하는 Swift 라이브러리

>[!div class="nextstepaction"]
>[Azure 의료 Api 개요](healthcare-apis-overview.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다. 