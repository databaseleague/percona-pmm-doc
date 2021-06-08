# Percona Monitoring and Management (PMM) Documentation

[![Netlify Status](https://api.netlify.com/api/v1/badges/52970fb0-4de9-4b89-ab41-bd1cf83564b4/deploy-status)](https://app.netlify.com/sites/pmm-doc/deploys)

[Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management) is a database monitoring solution that is free and open-source.

This repo holds the source files for the PMM technical documentation published at <https://www.percona.com/doc/percona-monitoring-and-management/>.

To contribute to that documentation, you can:

- **report a general problem** -- open an [Issue in this repo](https://github.com/percona/pmm-doc/issues/new?title=PMM%20doc%20issue&body=Please%20describe%20the%20issue%20here) or use [Percona's Jira](https://jira.percona.com/browse/PMM).

- **report a problem on a page** -- every page of our documentation has a link, *Report a problem with this page*, a shortcut to this repo's *Issues*. (The link pre-fills the issue's subject so we know what page you're on.) Click it, explain it, and we'll fix it.

- **fix a problem yourself** -- there is also an *Edit this page* link that will bring you to this repo to edit the Markdown source file for that page. Make your changes (you'll have to fork the repo unless you're Percona staff) and submit a PR which we'll review and adjust where necessary before merging and publishing. If the changes are more than a few lines, you might want to build the website locally to see how it looks in context. That's what the rest of this README covers.

> ![PMM Technical Documentation links](docs/_images/pmm-links.png)
>
> *Links on the [PMM Technical Documentation home page](https://www.percona.com/doc/percona-monitoring-and-management/)*

## Introduction

We use [MkDocs](https://www.mkdocs.org/) to convert [Markdown](https://daringfireball.net/projects/markdown/) files into a static HTML website (or [PDF](#pdf)). This process is called [*building the documentation*](#building-the-documentation).

The documentation source files are in the `docs` directory. (Other files in this repo are explained in [Directories and files](#directories-and-files).)

The two major PMM versions are kept in separate branches:

- `main` is for PMM 2.x (latest)
- `1.x` is for PMM 1.x

Before you start, it helps to know what [git](https://git-scm.com), [Python 3](https://www.python.org/downloads/) and [Docker](https://docs.docker.com/get-docker/) are, what [Markdown](https://daringfireball.net/projects/markdown/) is and how to write it, and how to install and use those things on the command line. (If you don't, consider opening an [Issue](https://github.com/percona/pmm-doc/issues/new?title=PMM%20doc%20issue&body=Please%20describe%20the%20issue%20here) instead.)

## Building the documentation

If you'd like to have a local copy of PMM documentation, or are thinking about contributing, it helps if you can build the documentation to see how it will look when published. The easiest way is to use Docker, as this avoids having to install MkDocs and its dependencies.

### With Docker

1. [Get Docker](https://docs.docker.com/get-docker/)
2. Clone this repository
3. Change directory to `pmm-doc`
4. Use [our Docker image](https://hub.docker.com/repository/docker/perconalab/pmm-doc-md) to *build the documentation*:

    ```sh
    docker run --rm -v $(pwd):/docs perconalab/pmm-doc-md mkdocs build
    ```

5. Find the `site` directory, open `index.html` in a browser to view the first page of documentation.

If you want to see how things look as you edit, MkDocs has a built-in server for live previewing. After (or instead of) building, run:

```sh
docker run --rm -v $(pwd):/docs -p 8000:8000 perconalab/pmm-doc-md mkdocs serve --dev-addr=0.0.0.0:8000
```

Wait until you see `INFO    -  Start detecting changes` then point your browser to [http://0.0.0.0:8000](http://0.0.0.0:8000).

### Without Docker

*If you don't use Docker, you must install MkDocs and all its dependencies.*

1. Install [Python 3](https://www.python.org/downloads/)

2. Install MkDocs and required extensions:

    ```sh
    pip install -r requirements.txt
    ```

3. Build the site:

    ```sh
    mkdocs build
    ```

4. Open `site/index.html`

Or, to run the built-in web server:

```sh
mkdocs serve
```

View the site at <http://0.0.0.0:8000>

## PDF

*How to create a PDF version of the documentation.*

1. (For Percona staff) If bulding for a release of PMM, edit `mkdoc.yml` and change:

    - The release number in `plugins.with-pdf.output_path`
    - The release number and date in `plugins.with-pdf.cover_subtitle`

2. Build

    - With Docker:

        ```sh
        docker run --rm -v $(pwd):/docs -e ENABLE_PDF_EXPORT=1 perconalab/pmm-doc-md mkdocs build -f mkdocs-pdf.yml
        ```

    - Without:

        ```sh
        ENABLE_PDF_EXPORT=1 mkdocs build -f mkdocs-pdf.yml
        ```

3. The PDF is in `site/_pdf`.

## Directories and files

- `mkdocs-base.yml`: Base MkDocs configuration file. Contains common configuration and navigation used by other MkDocs configs.
- `mkdocs-pdf.yml`: MkDocs configuration file. Creates themed [PDF](#pdf). Inherits from `mkdocs-base.yml`.
- `mkdocs-percona.yml`: MkDocs configuration file. Creates unthemed HTML for hosting on percona.com. Inherits from `mkdocs-base.yml`.
- `mkdocs.yml`: Default MkDocs configuration file. Creates (Material) themed HTML for hosting anywhere. Inherits from `mkdocs-base.yml`.
- `docs`:
    - `*.md`: Markdown files.
    - `_images/*`: Images.
    - `css`: Styling.
    - `js`: JavaScript files.
- `_resources`:
    - `bin`
        - `glossary.tsv`: Export from a spreadsheet of glossary entries.
        - `make_glossary.pl`: Script to write Markdown page from `glossary.tsv`.
        - `grafana-dashboards-descriptions.py`: Script to extract dashboard descriptions from <https://github.com/percona/grafana-dashboards/>.
        - `plantuml`: Wrapper script for running PlantUML.
        - `plantuml.jar`: Copy of the PlantUML Java file (needed for Netlify builds).
    - `diagrams`:
        - `*.puml`: [PlantUML](https://plantuml.com) diagrams (see comments inside each).
        - `plantuml_styles.uml`: Global style for PlantUML diagrams.
    - `templates`: Stylesheet for PDF output (used by [mkdocs-with-pdf](https://github.com/orzih/mkdocs-with-pdf) extension).
    - `theme`:
        - `main.html`: MkDocs template for HTML published on percona.com.
- `requirements.txt`: Python package dependencies.
- `runtime.txt`: Python version specifier (used by netlify).
- `variables.yml`: Values used throughout the Markdown, including the current PMM version/release number.
- `netlify.toml`: Netlify build definition.
- `.spelling`: Words regarded as correct by `mdspell` (See [Spelling and grammar](#spelling-and-grammar).)
- `.github`:
    - `workflows`:
        - `build.yml`: Workflow specification for building the documentation via a GitHub action. (Uses `mike` which puts HTML in `publish` branch.)
- `site`: When building locally, directory where HTML is put.

## Version switching

We are trialing the use of [mike](https://github.com/jimporter/mike) to build different versions.

With this, a [GitHub actions](https://github.com/percona/pmm-doc/actions) workflow runs `mike` (which runs `mkdocs`). The HTML is committed and pushed to the `publish` branch. The whole branch is then copied (by us, naturally) to our web server.

## Image overlays

`docs/using/interface.md` uses an image of the home dashboard overlaid with numbered boxes to identify menu bars and control. This approach means the home dashboard image and its numbered version always look the same. You need only recreate the home page image in 1280x1280 format, then merge with the numbered overlay.

Here's how it's done.

- `PMM_Home_Dashboard.jpg` is created by [pmm-screenshots-pw](https://github.com/PaulJacobs-percona/pmm-screenshots-pw). If snapped by hand, it should be 1280x1280 pixels, to match the overlay image.

- `PMM_Home_Dashboard_Overlay.png` is exported from `_resources/diagrams/PMM_Home_Dashboard_Overlay.drawio` using <https://app.diagrams.net/>.

    1. Go to <https://app.diagrams.net/>
    2. If it's your first time, select *Device* at the *Save diagrams to:* dialog
    2. Click *Open existing diagram*
    3. Navigate to `pmm-doc/_resources/diagrams` and select `PMM_Home_Dashboard_Overlay.drawio`
    4. If the dashboard layout has changed, replace the *Guide* Layer with a new screenshot and adjust the elements on the *Overlay* layer as needed (To show layers, click View --> Layers). Untick the *Guide* Layer so it is not exported.
    5. Click File --> Export as --> PNG
    6. In the *Image settings* dialog, use these settings:
        - *Zoom*: 100%, Border Width: 0
        - *Size:* Page (The page dimensions in inches should be as close to the base image as possible, i.e. 1280x1280)
        - *Transparent Background:* ON
        - *Shadow:* OFF
        - *Grid*: OFF
        - *Include a copy of my diagram:* OFF
    7. Click *Export*
    8. Click *Device*
    9. Navigate to `pmm-doc/docs/_resources/diagrams` and click `PMM_Home_Dashboard_Overlay.png`
    10. Click *Save* and overwrite the current file

The overlay image is merged with a copy of the latest home dashboard using [`composite`](https://imagemagick.org/script/composite.php), one of the [ImageMagick](https://imagemagick.org/script/download.php) tools.

```sh
composite _resources/diagrams/PMM_Home_Dashboard_Overlay.png docs/_images/PMM_Home_Dashboard.jpg docs/_images/PMM_Home_Dashboard_Numbered.png
```

## Spelling and grammar

The GitHub actions build job performs a basic spell and grammar check. You can do these yourself on the command line if you have [Node.js](https://nodejs.org/en/download/) installed.

    npm i markdown-spellcheck -g
    mdspell --report --en-us --ignore-acronyms --ignore-numbers docs/<path to file>.md

To check all files:

    mdspell --report --en-us --ignore-acronyms --ignore-numbers "docs/**/*.md"

Add any custom dictionary words to `.spelling`. If spell checking fails, the GitHub action will fail too, but after the MkDocs build and so can be safely ignored. The `publish` branch will still have the latest build and can be used. Meanwhile, see what the spelling error is and either fix it or add the word to `.spelling`.

Grammar is checked using [`write-good`](https://github.com/btford/write-good). (The results of this check are ignored and don't affect the GitHub action.)

    npm i write-good -g
    write-good docs/<path to file>.md

To check all files:

    write-good docs/**/*.md

## Link checking

We're using the `mkdocs-htmlproofer-plugin` link checking plugin to detect broken URLs. It works great but increases the build time significantly (by between 10 and 50 times longer).

The plugin is installed in [our Docker image](https://hub.docker.com/repository/docker/perconalab/pmm-doc-md) and by the GitHub action but it is commented out in `mkdocs.yml`.

To enable it for local builds, uncomment the line with `htmlproofer` in the `plugins` section of `mkdocs.yml` and parse the build output for warnings.