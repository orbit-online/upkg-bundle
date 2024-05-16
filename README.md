# upkg-bundle

A GitHub action to bundle μpkg tarball packages.

## Usage

```
name: Release

on:
  push:
    tags: ['v*']

jobs:
  release:
    permissions:
      contents: write
    runs-on: ubuntu-latest
    name: Create GitHub release
    steps:
    - uses: actions/checkout@v4
      with:
        ref: ${{ github.ref }}
    - name: Get release notes
      id: release
      uses: orbit-online/git-release@v1
    - uses: orbit-online/upkg-bundle@v1
    - name: Create Release
      uses: ncipollo/release-action@v1
      with:
        name: ${{ steps.release.outputs.tag }}
        body: ${{ steps.release.outputs.message }}
        draft: false
        prerelease: false
        artifactErrorsFailBuild: true
```

### Inputs

| Name                | Description                                                                 | Default                                                                                     |
| ------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `upkg-version`      | The version of μpkg to use for bundling                                     | The one already installed or `latest` (to always get `latest`, set this input explicitly)   |
| `version`           | The version to write to upkg.json, corresponds to `-V` in `upkg bundle`     | [`program-version.sh "${{ github.ref }}"`](https://github.com/orbit-online/program-version) |
| `dest`              | The package destination, corresponds to `-d` in `upkg bundle`               | None, the μpkg default                                                                      |
| `paths`             | Argument (space separated) list of relative paths to include in the package | None, the μpkg default                                                                      |
| `working-directory` | The working directory to change to before bundling                          | `.`                                                                                         |

### Outputs

| Name       | Description                                 |
| ---------- | ------------------------------------------- |
| `dest`     | The location of the bundled package         |
| `basename` | The filename of the bundled package         |
| `sha256`   | The SHA-256 checksum of the bundled package |
