# RedMart Partner API Documentation using Slate

Marketplace Sellers (or Partners) at RedMart can integrate their platform with our own using this documentation.

<p align="center"><img src="https://raw.githubusercontent.com/Redmart/partner-api-doc/master/source/images/redmart.partner.api.screenshot.png" width=700 alt="Screenshot of Documentation created with Slate"></p>

<p align="center"><em>Check it out at <a href="https://redmart.github.io/partner-api-doc">redmart.github.io/partner-api-doc</a></em></p>

## Viewing the Documentation locally

### Prerequisites

 - **Ruby, version 2.3.1 or newer**
 - **Bundler** — If Ruby is already installed, but the `bundle` command doesn't work, just run `gem install bundler` in a terminal.

### Getting Set Up

1. Clone this repository on your local machine.
2. `cd partner-api-doc`
3. At first use, initialize Slate
```shell
bundle install
```
4. Start Slate locally:
```shell
bundle exec middleman server
```
You can now see the docs at http://localhost:4567

## Updating the Documentation

### Prerequisites

 - **npm** - run `sudo npm i -g npm` to update [npm](https://www.npmjs.com/get-npm) to the latest version

### Getting Set Up

1. Install [Widdershins](https://github.com/Mermade/widdershins) (converts a swagger file into a [Slate](https://github.com/lord/slate/wiki/Slate-Related-Tools#converting-openapi--swagger-definitions-to-slate-compatible-markdown) markdown)
```shell
sudo npm install -g widdershins
``` 
2. Get the `mp-partner-api-service-vX.json` Swagger file from [Confluence](https://redmart.atlassian.net/wiki/spaces/marketplace/pages/407470169/2018Q1+-+Stock+Management) (last [known version](https://gist.githubusercontent.com/JanGe/43b539746d1ee57fe4e92c0c358b37fa/raw/d92c053f6304e85ef63e6b950e3e46df7c576b33/mp-partner-api-service-v1.json) as of July 2018)
3. Generate a Slate markdown out of the Swagger file
```shell
widdershins mp-partner-api-service-v1.json --language_tabs "shell:Shell" --language_tabs "java:Java" -o mp-partner-api-slate.md
```
4. Use the generated `mp-partner-api-slate.md` _as a scaffolding only_ to update this repo's `source/index.html.md`. For now, the Swagger does not translate directly to the markdown, which needs a fair amount of manual editing. See [editing Slate markdown](https://github.com/lord/slate/wiki/Markdown-Syntax) for more syntax details.
5. As you edit, you can view your changes locally in a browser, see [previous section](#viewing-the-documentation-locally)
6. Once your changes are finished, create a PR (or push directly to master)
7. run this repo's deploy script to make your changes visible at [redmart.github.io/partner-api-doc](https://redmart.github.io/partner-api-doc)
```shell
cd partner-api-doc
./deploy.sh
```

The deploy script essentially generates the documentation's html/js files and commits them to the repo's [gh-pages](https://github.com/Redmart/partner-api-doc/tree/gh-pages) branch. The latter is used as the source for the Documentation's [github-hosted page](https://redmart.github.io/partner-api-doc) (see [Settings -> Options -> GitHub Pages](https://github.com/Redmart/partner-api-doc/settings) for more details).

