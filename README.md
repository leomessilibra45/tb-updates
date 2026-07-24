# tb-updates — in-app update manifests

Each app reads ONE version.json. Its path is baked into that app's build
(Main.cpp -> nUpdateUrl). To push an update to an app, edit only ITS file.

## Layout
- version.json            = BT KERNEL (my main app). Its build reads the ROOT file. DO NOT MOVE.
- _template/version.json  = copy this to start a new friend
- FRIEND/version.json     = one folder per friend app (e.g. friendA/, friendB/)

## version.json fields (all read by the app)
| field               | meaning |
| ------------------- | ------- |
| version             | new version string. If it differs from the installed versionName -> update popup |
| url                 | direct download link to the new APK (a GitHub Release asset) |
| force_update        | true = user is BLOCKED until they update |
| maintenance         | true = whole app is locked with a maintenance screen |
| maintenance_message | text shown on the maintenance screen |
| announce            | true = show a dismissible announcement popup |
| announce_message    | text shown in the announcement |
| channel             | Telegram link shown on maintenance/announcement screens |

## Add a NEW friend (later)
1. Make a folder FRIEND/ and copy _template/version.json into it.
2. Build his APK with: his package name, his branding (name/icon/telegram),
   nUpdateUrl in Main.cpp -> .../contents/FRIEND/version.json,
   and the SAME signing key (release_key.jks) so login/signature stays valid.
3. Upload his first APK as a GitHub Release asset; put that link in FRIEND/version.json.
4. To update him later: change version + url in FRIEND/version.json only.

## RULES that break updates if ignored
- The update APK MUST use the SAME signing key as the installed app,
  or Android refuses the install (signatures don't match).
- The update APK MUST have the SAME package name as the installed app.
- version must DIFFER from the installed versionName or no popup shows.
- Always ship the RELEASE-signed (packed) APK, never a -debug- build.
