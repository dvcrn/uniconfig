# UniConfig

Mackup inspired tool to sync application settings, powered by unison


## What it is 

UniConfig helps you keep your application settings (plists, config folders, licenses) synchronized across machines. It does this by using `unison` to diff files, then copies them into a synced folder of your choice

## How is this different from mackup? 

UniConfig does not use symlinks like mackup. So instead of symlinking things into Dropbox/iCloud, UniConfig uses `unison` for diffing and copies the actual files. 

### Why?

I like unison and ran into issues where symlinks randomly broke without me noticing it. I wanted the file content to be identical no matter where it is, including the actual application settings folder.

The sync folder in the cloud acts as a backup and source for diffing, but the apps would continue to work as is if those were deleted.

## Install & Usage 

Install `unison` and make sure it's in your path

```
go install github.com/dvcrn/uniconfig@latest
```

Create a config file in ~/.config/uniconfig/uniconfig.yaml:

```yaml
targetPath: ~/.config/appconfigsync
apps:
  - dash
  - raycast
```

### Usage
```
Commands:
  sync - run sync between all enabled apps
  init-from-target - run initial sync, targetPath -> appPath
  init-from-app - run initial sync, appPath -> targetPath
```

The initial sync options **force** one way. For example if you want your local configuration to be overwritten on initial sync with whatever you have in your storage folder, run `init-from-target`

## How to add support for new apps

Check out `apps/` in this repository. Apps are simple yaml files that explain what has to be copied:

```yaml
name: Raycast
friendlyName: raycast
files:
  - basePath: ~/Library/Preferences/
    includedFiles:
      - com.raycast.macos.plist
    ignoredFiles:
      - Name somethingToIgnore.plist
```

- `basePath` is the path that contains the files to sync. It can be `~/Library/Preferences/` when the file in question is just a preferences file, but also soemthing like `~/Library/Application Support/Dash`
- `includedFiles` are all files **within** basePath to process
- `ignoredFiles` is the opposite of `includedFiles` - stuff you don't want to get processed. Has to be Name, Path, BelowPath or Regex

## Troubleshooting & Caveats

- Apps that are currently running will override the config files usually when they're quit