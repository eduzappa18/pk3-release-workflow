# PK3 release workflow


This workflow will zip the repository files into a pk3 file (ignoring hidden filenames starting with a dot)  
and attach it to the asset list after a new release.

The format for the name of the pk3 is: `<repo-name>_<release-tag>.pk3`  
For example: **pk3-release-workflow_v1.0.0.pk3** (check [semver.org](http://semver.org/) for more info about tags)  
Spaces and non-alphanumeric characters will be replaced by dots (github limitation)

To use it just create a YAML file with the following code and place it inside the `.github/workflows` directory of your repository.  
Example: [.github/workflows/pk3.yml](.github/workflows/pk3.yml)

Result: [pk3-release-workflow/releases](https://github.com/eduzappa18/pk3-release-workflow/releases)


```yaml
name: Create PK3 release

on:
  release:
    types:
      - created

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Set PK3 Filename
      run: echo "PK3_FILENAME=${{ github.event.repository.name }}_${{ github.ref_name }}.pk3" >> $GITHUB_ENV

    - name: Zip Files
      run: zip -r "${{ env.PK3_FILENAME }}" . -x ".*"

    - name: Release
      uses: softprops/action-gh-release@v1
      if: startsWith(github.ref, 'refs/tags/')
      with:
        files: "${{ env.PK3_FILENAME }}"
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

```
