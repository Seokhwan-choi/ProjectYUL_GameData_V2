# ProjectYUL GameData V2

Public data-only repository for the new Android and iOS app. The app reads this fixed manifest URL:

https://raw.githubusercontent.com/Seokhwan-choi/ProjectYUL_GameData_V2/main/deploy/manifest.json

## Repository contents

- deploy/manifest.json: current platform and contract entries.
- GitHub Releases: one DataSheet.zip asset per unique data-c{contract}-r{revision} tag.

Do not upload source code, XLSX files, credentials, signing keys, or HMAC secrets. The ZIP is encrypted, but the client contains the decryption and HMAC key; encryption does not make this repository a secret store. HMAC detects changes. It is not a server-only signature.

## First release

1. Create this repository as Public, with the default branch named main.
2. Add README.md and deploy/manifest.json using the supplied manifest file.
3. In Unity, choose Yul → 새 저장소용 데이터 배포 파일 생성. The tool validates current tables and writes a versioned DataSheet.zip, manifest-entry.json, and 배포-요약.txt under Builds/GameDataV2/data-c{contract}-r{revision}.
4. Create a GitHub Release with the exact generated tag, such as data-c1-r1. Attach only that folder's DataSheet.zip and publish the Release.
5. Copy both generated platform entries into deploy/manifest.json. Set a new positive generation, UTC publication/expiry times, and the real ZIP hash. Publish the manifest commit after the Release asset is public.
6. Confirm the repository is readable without authentication, then test download and startup in the new app.

The supplied manifest contains an all-zero placeholder hash. It is not operational. Replace it after the first ZIP Release is public.

## Manifest updates

- For a data correction within the same contract, use a never-before-used higher revision and a new Release tag.
- For a contract change, increase contractVersion; do not label current data with an older contract.
- Increase generation for every manifest change. Keep all other supported platform/contract entries.
- Release the ZIP first. Then update the manifest with its exact SHA-256. Do not reuse tags or revisions.
- minAppBuild is the minimum Android versionCode or iOS buildNumber. Use the new app's actual values.
- Set validUntilUtc 30 days after publication. Review renewal seven days before expiry. The client uses the HTTPS response Date header when available.
- Raw-content CDN caching can delay a manifest change. This does not guarantee an immediate update or provide a server-authoritative purchase/reward gate.
- To stop a platform/contract, keep its entry and set enabled to false.

Each ZIP must contain only a root DataSheet.txt. The client accepts manifestVersion 1, payloadVersion 1 and the matching contract/revision.
