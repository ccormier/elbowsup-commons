# elbowsup-commons — patched Fossify Commons

A fork of [Fossify Commons](https://github.com/FossifyOrg/Commons) used by the
Elbows Up dialer. It removes the anti-tamper checks and fixes hard-coded
`org.fossify.*` assumptions that break renamed forks.

- **Base:** upstream tag `6.1.6`
- **Published as:** `org.fossify:commons:6.1.6-elbowsup1` (via mavenLocal)
- **Upstream:** https://github.com/FossifyOrg/Commons (remote `upstream`)

## Publish

```bash
printf 'sdk.dir=/path/to/android-sdk\n' > local.properties
./gradlew :commons:publishToMavenLocal -PVERSION=6.1.6-elbowsup1
```

## Patches (re-apply on every new upstream tag)

1. `extensions/Activity.kt`
   - delete `showModdedAppWarning()`
   - `checkAppSideloading()` sets `appSideloadingStatus = SIDELOADING_FALSE` and returns false
   - `launchCallIntent` uses `setClassName(packageName, "org.fossify.phone.activities.DialerActivity")`
2. `compose/extensions/ActivityExtensions.kt` — delete `FAKE_VERSION_APP_LABEL` and `fakeVersionCheck()`
3. `compose/extensions/ComposeActivityExtensions.kt` — delete `FakeVersionCheck()`
4. `compose/theme/AppTheme.kt` — remove `OnContentDisplayed()` and its call
5. `activities/BaseSimpleActivity.kt` — remove the `onCreate` modded-app block and the `startCustomizationActivity()` guard
6. `activities/CustomizationActivity.kt` — remove the `pickPrimaryColor()` guard
7. `helpers/MyContactsContentProvider.kt` — drop the caller-package allowlist
8. `activities/ManageBlockedNumbersActivity.kt` — recognise `com.keejii.elbowsup`
9. `extensions/Context.kt` — `isDefaultDialer()` uses the real role check

## Rebasing onto a new upstream Commons tag

```bash
git fetch upstream --tags
git switch -C main <new-tag>          # re-apply the patches above
./gradlew :commons:publishToMavenLocal -PVERSION=<new-tag>-elbowsup1
git push --force-with-lease origin main
```

Then bump the `commons` pin in the app's `gradle/libs.versions.toml`.
