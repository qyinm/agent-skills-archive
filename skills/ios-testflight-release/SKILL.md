---
name: ios-testflight-release
description: Archive an iOS app with Xcode and upload it to TestFlight using xcodebuild.
metadata:
  tags: ios, xcode, testflight, app-store-connect, deployment, release
---

# iOS TestFlight Release

Use this skill when the user wants to archive an iOS app and upload the resulting build to TestFlight.

## What this skill covers

- Inspecting the repo for the correct `.xcodeproj` or `.xcworkspace`, scheme, target, and signing setup
- Verifying build metadata like bundle identifier, marketing version, and build number
- Creating a Release archive with `xcodebuild`
- Exporting an `.ipa` for App Store Connect or uploading directly during export
- Handling common TestFlight blockers like reused build numbers and missing signing assets

## Preconditions

Before uploading, confirm all of the following:

- Xcode is installed and selected with `xcode-select`
- The machine has access to an Apple Developer account in Xcode, or you have an App Store Connect API key
- Automatic or manual signing is already configured for the app target
- The build number is higher than the last uploaded build for the same marketing version

## Recommended workflow

### 1. Discover the project shape

Find the project or workspace and any release automation already present.

```bash
ls
xcodebuild -list -project "App.xcodeproj"
# or
xcodebuild -list -workspace "App.xcworkspace"
```

Also inspect for existing release helpers like:

- `Fastfile`
- `README.md`
- CI workflows
- `ExportOptions.plist`

### 2. Read signing and version settings

Check the build settings for the Release configuration.

```bash
xcodebuild -showBuildSettings -project "App.xcodeproj" -scheme "App" -configuration Release
```

Focus on these values:

- `PRODUCT_BUNDLE_IDENTIFIER`
- `MARKETING_VERSION`
- `CURRENT_PROJECT_VERSION`
- `DEVELOPMENT_TEAM`
- `CODE_SIGN_STYLE`

### 3. Archive the app

Use a generic iOS destination and an explicit archive path.

```bash
xcodebuild \
  -project "App.xcodeproj" \
  -scheme "App" \
  -configuration Release \
  -destination "generic/platform=iOS" \
  -archivePath "$PWD/build/App.xcarchive" \
  -allowProvisioningUpdates \
  archive
```

If the project uses a workspace, replace `-project` with `-workspace`.

### 4. Export locally first when you want a safe validation step

Create an export options plist like this:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>destination</key>
  <string>export</string>
  <key>manageAppVersionAndBuildNumber</key>
  <false/>
  <key>method</key>
  <string>app-store</string>
  <key>signingStyle</key>
  <string>automatic</string>
  <key>teamID</key>
  <string>YOUR_TEAM_ID</string>
</dict>
</plist>
```

Then export:

```bash
xcodebuild \
  -exportArchive \
  -archivePath "$PWD/build/App.xcarchive" \
  -exportPath "$PWD/build/export" \
  -exportOptionsPlist "/tmp/AppExportOptions.plist"
```

### 5. Upload to TestFlight with xcodebuild

Use `destination = upload` in the export options plist.

Export option keys and accepted values are somewhat Xcode-version sensitive. For broad compatibility, prefer `method = app-store`. Some newer Xcode flows may accept `app-store-connect`, but that value is not as portable.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>method</key>
  <string>app-store</string>
  <key>destination</key>
  <string>upload</string>
  <key>signingStyle</key>
  <string>automatic</string>
  <key>teamID</key>
  <string>YOUR_TEAM_ID</string>
  <key>uploadSymbols</key>
  <true/>
  <key>manageAppVersionAndBuildNumber</key>
  <false/>
</dict>
</plist>
```

Upload with:

```bash
xcodebuild \
  -exportArchive \
  -archivePath "$PWD/build/App.xcarchive" \
  -exportPath "$PWD/build/testflight-upload" \
  -exportOptionsPlist "/tmp/AppTestFlightExportOptions.plist" \
  -allowProvisioningUpdates
```

If an App Store Connect API key is preferred, pair this command with:

- `-authenticationKeyPath`
- `-authenticationKeyID`
- `-authenticationKeyIssuerID`

For headless or repeatable automation, App Store Connect API key authentication is preferred over Apple ID login. Apple ID auth can be brittle and may require interactive verification.

### 6. Signing fork: automatic vs manual

Archive success does not guarantee export or upload success. Many failures happen later when Xcode tries to re-sign for distribution.

- `automatic`: use `-allowProvisioningUpdates` during archive and upload so Xcode can fetch or refresh signing assets
- `manual`: provide `provisioningProfiles` in the export options plist, keyed by bundle identifier, and ensure the matching distribution certificate is available locally

Example manual-signing export fragment:

```xml
<key>signingStyle</key>
<string>manual</string>
<key>provisioningProfiles</key>
<dict>
  <key>com.example.app</key>
  <string>App Store Profile Name</string>
</dict>
```

## Build number policy

App Store Connect rejects duplicate `CFBundleVersion` values for the same `CFBundleShortVersionString`.

If upload fails with a message like:

`The bundle version must be higher than the previously uploaded version`

then bump the app's build number, re-archive, and retry the upload.

In many Xcode projects, `CURRENT_PROJECT_VERSION` is the source for `CFBundleVersion`, but that is not universal. Verify how the app sets its build number before editing release metadata.

Once a build number has been used for a given marketing version in App Store Connect, it cannot be reused even if the older build is removed.

## App Store Connect record requirement

The app record and bundle identifier must already exist in App Store Connect and match the archive being uploaded. If the bundle identifier is missing or mismatched, the upload can fail even when signing is correct.

## Verification checklist

After a successful run, verify these signals in command output:

- `** ARCHIVE SUCCEEDED **`
- `** EXPORT SUCCEEDED **`
- `Uploaded package is processing`
- `Upload succeeded`

Then confirm in App Store Connect that the build moves from `Processing` to `Ready to Test`.

## Troubleshooting

### Missing signing assets

- Check `security find-identity -v -p codesigning`
- Use `-allowProvisioningUpdates` when automatic signing is configured
- Confirm the correct team is selected in the project

### Duplicate build number

- Increase the app's `CFBundleVersion` source, often `CURRENT_PROJECT_VERSION`
- Rebuild the archive
- Retry upload

### Wrong project container

- If the repo uses CocoaPods or SwiftPM workspace integration, prefer `.xcworkspace`
- If `xcodebuild -list` only shows the project scheme, `.xcodeproj` is sufficient

### Authentication uncertainty

- First check whether Xcode already has a signed-in developer account on the target team
- If not, use an App Store Connect API key instead of guessing credentials

## Reporting back to the user

When the workflow completes, report:

- The scheme used
- The archive path
- The uploaded version and build number
- Whether the build is processing or ready in TestFlight
- Any repo changes made only for release metadata, such as a build-number bump
