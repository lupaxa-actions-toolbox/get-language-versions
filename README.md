<p align="center">
    <a href="https://github.com/lupaxa-actions-toolbox">
        <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/organisations/actions-toolbox/readme-logo.png" alt="Organisation Logo" />
    </a>
</p>

<h1 align="center">Get Language Versions</h1>

GitHub Action that returns the latest runtime versions for a language. Use the result as one version, or load it with `fromJson` as a matrix.

It reads the version list from the latest release of that language's setup action when a release exists. Pass `use-head: true` to read the default branch instead.

| Language | Setup Action                                                   | Version Source                                                         |
| :------- | :------------------------------------------------------------- | :--------------------------------------------------------------------- |
| Go       | [setup-go](https://github.com/actions/setup-go)                | [go-versions](https://github.com/actions/go-versions)                  |
| Node     | [setup-node](https://github.com/actions/setup-node)            | [node-versions](https://github.com/actions/node-versions)              |
| Perl     | [setup-perl](https://github.com/shogo82148/actions-setup-perl) | [actions-setup-perl](https://github.com/shogo82148/actions-setup-perl) |
| PHP      | [setup-php](https://github.com/shivammathur/setup-php)         | [phpreleases](https://phpreleases.com/api/releases/)                   |
| Python   | [setup-python](https://github.com/actions/setup-python)        | [python-versions](https://github.com/actions/python-versions)          |
| Ruby     | [setup-ruby](https://github.com/ruby/setup-ruby)               | [setup-ruby](https://github.com/ruby/setup-ruby)                       |

## Usage

```yaml
- uses: lupaxa-actions-toolbox/get-language-versions@master
  id: get-versions
  with:
    language: "python"
    min-version: "3.8"
    max-version: "3.13"
    max-versions: "0"
    include-prereleases: true
    highest-only: false
    remove-patch-version: false
    use-head: false
```

Read the result from `steps.get-versions.outputs.latest-versions`.

## Inputs

| Input                | Required | Default | Accepted Values                                                               |
| :------------------- | :------- | :------ | :---------------------------------------------------------------------------- |
| language             | Yes      |         | go, node, nodejs, perl, php, python, ruby                                     |
| min-version          | No       | eol     | semver, eol, or all                                                           |
| max-version          | No       | latest  | semver or latest                                                              |
| max-versions         | No       | 0       | 0 keeps every match. A positive number keeps that many newest versions.       |
| include-prereleases  | No       | false   | true or false                                                                 |
| highest-only         | No       | false   | true returns one version. false returns a JSON list.                          |
| remove-patch-version | No       | false   | true or false. Cannot be combined with include-prereleases.                   |
| use-head             | No       | false   | true reads the default branch instead of the latest release. Ignored for PHP. |

## Matrix Example

```yaml
jobs:
  get-versions:
    runs-on: ubuntu-latest
    outputs:
      version-matrix: ${{ steps.get-language-versions.outputs.latest-versions }}
    steps:
      - uses: lupaxa-actions-toolbox/get-language-versions@master
        id: get-language-versions
        with:
          language: "python"
          min-version: "3.8"
          include-prereleases: true

  test:
    needs: get-versions
    runs-on: ubuntu-latest
    strategy:
      matrix:
        version: ${{ fromJson(needs.get-versions.outputs.version-matrix) }}
    steps:
      - uses: actions/setup-python@6d4fd22b20465ccc0f7354476588e8df7c9ca0e0  # v7.1.0
        with:
          python-version: ${{ matrix.version }}
```

## Single Version Example

```yaml
jobs:
  get-versions:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.get-language-versions.outputs.latest-versions }}
    steps:
      - uses: lupaxa-actions-toolbox/get-language-versions@master
        id: get-language-versions
        with:
          language: "python"
          highest-only: true

  test:
    needs: get-versions
    runs-on: ubuntu-latest
    steps:
      - uses: actions/setup-python@6d4fd22b20465ccc0f7354476588e8df7c9ca0e0  # v7.1.0
        with:
          python-version: ${{ needs.get-versions.outputs.version }}
```

<a href="https://github.com/the-lupaxa-project">
    <img src="https://raw.githubusercontent.com/the-lupaxa-project/brand-assets/master/logos/components/footer-for-child-orgs.svg" alt="The Lupaxa Project Footer" width="100%" />
</a>
