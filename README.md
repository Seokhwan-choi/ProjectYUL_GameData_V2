# ProjectYUL GameData V2

Public data-only repository for the new Android and iOS app. The app reads:

https://raw.githubusercontent.com/Seokhwan-choi/ProjectYUL_GameData_V2/main/deploy/manifest.json

## One-time setup

1. Create this repository as Public with main as its default branch.
2. Add this README and deploy/manifest.json. The supplied manifest has an empty entries array; it is a bootstrap file and cannot start the app.
3. In Unity Project Settings, set new Android and iOS application identifiers and the first app build numbers.
4. Run the deployment generator.

## Generate a deployment

In Unity, choose Yul/새 저장소용 데이터 배포 파일 생성, then click 다음 배포 파일 생성.

Or close Unity Editor and run this in PowerShell. Change the Unity executable path if needed:

    & "C:\Program Files\Unity\Hub\Editor\6000.0.66f1\Editor\Unity.exe" -batchmode -quit -projectPath "D:\Project_YUL\Project_YUL" -executeMethod Yul.GameDataV2DeploymentCommand.GenerateNextRelease

The command downloads and validates the current public manifest. It picks the next unused revision and generation, sets a 30-day UTC validity period, preserves entries for other contracts, and writes these files under Builds/GameDataV2/data-c{contract}-r{revision}. The bootstrap manifest starts at generation 1 with no entries; the first data manifest uses generation 2:

- DataSheet.zip
- manifest-entry.json
- manifest.json — complete updated manifest, ready to publish
- 배포-요약.txt

The existing public manifest must be available at the fixed URL. The generator stops if it cannot read or validate it. It also requires Android and iOS application identifiers different from the legacy app ID. It does not upload or publish files.

Publish in this order:

1. Create a GitHub Release using the generated tag and attach DataSheet.zip.
2. Replace deploy/manifest.json with the generated manifest.json and commit it to main.
3. Check the raw manifest and release asset URLs.

The first generated revision may skip a number if that tag already exists in GitHub or its local output folder. Never reuse a release tag.

## Data and security

Do not upload source code, XLSX files, credentials, signing keys, or HMAC secrets. The ZIP is encrypted, but the client contains the decryption and HMAC key; encryption does not make this repository a secret store. HMAC detects changes. It is not a server-only signature.

## 숫자 고르는 법 (아주 쉽게)

앱은 공책을 읽는 아이고, ZIP은 공책입니다. 평소에는 생성기를 누르면 됩니다. 숫자는 직접 바꾸지 않습니다.

| 이름 | 쉬운 뜻 | 언제 바꾸나요? |
|---|---|---|
| appBuild | 앱의 빌드 번호 | 새 앱을 만들 때 올립니다. Android는 versionCode, iOS는 buildNumber입니다. |
| minAppBuild | 이 공책을 읽어도 되는 가장 오래된 앱 번호 | 생성기가 Unity의 Android/iOS 빌드 번호를 복사합니다. 앱 번호가 이보다 낮으면 업데이트가 필요합니다. |
| contractVersion | 앱과 공책 사이의 읽기 약속 번호 | 데이터 모양이나 뜻이 바뀌어 옛 앱이 이해하지 못할 때만 올립니다. 현재 값은 1입니다. |
| revision | 같은 약속 안에서 데이터 공책의 판 번호 | 데이터를 다시 배포할 때 생성기가 올립니다. 직접 정하지 않습니다. |
| generation | 게시판 안내문의 판 번호 | manifest를 새로 게시할 때 생성기가 올립니다. 빈 시작 manifest는 1, 첫 데이터 manifest는 2입니다. |
| tag | ZIP의 이름표 | contractVersion과 revision으로 자동 생성됩니다. 예: data-c1-r2. 직접 정하지 않습니다. |
| SHA-256 | ZIP의 지문 | ZIP을 만들 때 자동 계산됩니다. 직접 정하지 않습니다. |
| manifestVersion / payloadVersion | 안내문과 공책의 읽는 방법 번호 | 평소에는 1 그대로 둡니다. 통신 형식 자체를 바꿀 때만 개발자가 변경합니다. |

### 공책 내용만 고칠 때

엑셀 오타, 보상 수치, 번역처럼 내용만 바뀌고 데이터 모양은 그대로면 생성기를 실행합니다. contractVersion은 그대로입니다. revision, generation, tag, SHA-256, 날짜는 자동으로 바뀝니다.

주의: 생성기는 Unity PlayerSettings에 적힌 Android versionCode와 iOS buildNumber를 minAppBuild에 복사합니다. 예전 앱도 새 데이터를 읽어야 하면, 생성할 때 두 값에 현재 출시된 호환 앱의 번호를 둡니다. 아직 출시하지 않은 다음 앱 번호를 넣으면 기존 앱이 업데이트를 요구받습니다.

### 앱과 공책의 약속을 바꿀 때

새 열·키·ID 규칙 때문에 옛 앱이 공책을 읽지 못하면 contractVersion을 올리고, 그 약속을 아는 새 앱도 출시합니다. 새 약속의 revision은 보통 1부터 시작합니다. minAppBuild에는 그 데이터를 읽을 수 있는 최소 앱 번호가 들어갑니다. 생성기는 Unity에 적힌 번호를 복사합니다.

### 숫자 예시

- 처음 배포: contractVersion 1, revision 1, generation 2, tag data-c1-r1.
- 오타 수정 후 재배포: contractVersion은 1 그대로, revision 2, generation 3, tag data-c1-r2.
- 읽기 약속을 바꿔 새 앱이 필요한 배포: contractVersion 2, 새 약속의 revision 1, generation 4, tag data-c2-r1. 실제 번호는 현재 manifest를 읽은 생성기가 정합니다.

manifest만 직접 고치는 경우도 있습니다. 예를 들어 특정 플랫폼을 중지하려고 enabled를 false로 바꾸면 generation을 올리고 발행일·만료일을 갱신합니다. ZIP이 그대로면 revision과 tag는 바꾸지 않습니다.

## Later updates

- The generated manifest preserves other contract entries and replaces the Android/iOS entries for the current contract.
- Each generated manifest expires 30 days after publication. Run the generator for every update; it refreshes the dates and generation.
- Raw-content CDN caching can delay a manifest change. This does not guarantee an immediate update or provide a server-authoritative purchase/reward gate.
- To stop a platform/contract, keep its entry and set enabled to false.

Each ZIP contains only a root DataSheet.txt. The client accepts manifestVersion 1, payloadVersion 1, and the matching contract/revision.
