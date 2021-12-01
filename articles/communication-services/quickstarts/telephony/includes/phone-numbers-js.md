---
ms.openlocfilehash: 49e8c6600f93c1054aea9455684a6254664f34b9
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385396"
---
> [!NOTE]
> [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/phone-numbers-quickstart)에서 이 빠른 시작에 대한 최종 코드 칮기

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- 터미널 또는 명령 창에서 `node --version`를 실행하여 Node.js가 설치되어 있는지 확인합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

먼저 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

방금 만든 디렉터리의 루트에 **phone-numbers-quickstart.js** 라는 파일을 만듭니다. 다음 조각을 추가합니다.

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 전화 번호 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/communication-phone-numbers --save
```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 추가합니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

클라이언트 라이브러리에서 **PhoneNumbersClient** 를 가져와서 연결 문자열로 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

**phone-numbers-quickstart.js** 의 맨 위에 다음 코드를 추가합니다.

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>전화 번호 관리

### <a name="search-for-available-phone-numbers"></a>사용 가능한 전화 번호 검색

전화 번호를 구매하려면 먼저 사용 가능한 전화 번호를 검색해야 합니다. 전화 번호를 검색하려면 지역 번호, 할당 유형, [전화 번호 기능](../../../concepts/telephony/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [전화 번호 유형](../../../concepts/telephony/plan-solution.md#phone-number-types-in-azure-communication-services) 및 수량을 제공합니다. 수신자 부담 전화 번호 형식의 경우 지역 코드 제공은 선택 사항입니다.

`main` 함수에 다음 코드 조각을 추가합니다.

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = await searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>전화 번호 구매

전화 번호 검색의 결과는 `PhoneNumberSearchResult`입니다. 여기에는 검색 시 숫자를 얻기 위해 번호 구매 API에 전달할 수 있는 `searchId`가 포함됩니다. 전화 번호 구매 API를 호출하면 Azure 계정에 요금이 부과됩니다.

`main` 함수에 다음 코드 조각을 추가합니다.

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>전화 번호 기능 업데이트

이제 전화 번호를 구매한 후 다음 코드를 추가하여 해당 기능을 업데이트합니다.

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>구매한 전화 번호 가져오기

번호 구매 후에 클라이언트에서 검색할 수 있습니다. `main` 함수에 다음 코드를 추가하여 방금 구매한 전화 번호를 가져옵니다.

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log("These capabilities:", capabilities, "should be the same as these:", updateRequest, ".");
```

구매한 전화 번호를 모두 검색할 수도 있습니다.

```javascript
const purchasedPhoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of purchasedPhoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>전화 번호 해제

이제 구매한 전화 번호를 해제할 수 있습니다. `main` 함수에 아래 코드 조각을 추가합니다.

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await phoneNumbersClient.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>코드 실행

`node` 명령을 사용하여 **phone-numbers-quickstart.js** 파일에 추가한 코드를 실행합니다.

```console
node phone-numbers-quickstart.js
```