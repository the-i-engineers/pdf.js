# Pull the latest release of upstream into the fork (you need to be admin)
* fetch upstream `git fetch upstream`
* checkout master `git checkout master`
* consider squashing tie-changes if there are several to reduce conflict resolving steps
* rebase master to the latest release-tag of upstream (e.g. `git rebase v2.4.456`)
* resolve merge-conflicts
* force push master `git push -f origin master`
* change name in [package.json](package.json) to `tie-pdf.js` and version to `<upstream version>+tie.<tie version>` (e.g. `v2.4.456+tie.1`)
* commit and push change

# Make new changes
* checkout a new branch
* add changes (keep list of TIE changes in this md up to date)
* make a pull-request to merge your branch into master

# Create new release
* tag the last commit on master `tag -a v2.16.105+tie.1 -m "v2.16.105+tie.1"` and push it to Github `git push origin --tags`
* install gulp and execute `gulp generic-legacy`
* copy package.json into build/generic-legacy and remove all dependencies
* login to nexus with `npm --registry="http://dev-repository.tie.local:8081/repository/npm-hosted/" login`
* move into build/generic-legacy and publish with `npm --registry="http://dev-repository.tie.local:8081/repository/npm-hosted/" publish`

# Patch an existing release
* checkout the release-branch you need to patch
* bump tie-version in [package.json](package.json) (e.g. `v2.4.456+tie.2`)
* make your changes and commit a new change
* build and publish release like described above

# List of TIE changes on the fork of pdf.js
* remove console.log, console.warn and console.error from
  * [app.js](./web/app.js) search for `// Provides some basic debug information` and delete log
  * [ui_utils.js](./web/ui_utils.js) scrollIntoView
  * [l10n.js](./external/webL10n/l10n.js) search for `console.warn('#' +` and delete two log occurrences in getL10nData
* [KIS-1110](https://jira.tie.ch/browse/KIS-1110): set printResolution in defaultOptions of [app_options.js](./web/app_options.js) to 600
* [IE-4298](https://jira.tie.ch/browse/IE-4298): in [app.js](./web/app.js)
  * replace `const viewerOrigin = new URL(window.location.href).origin || "null";` with `const viewerOrigin = "null";`
  * replace `PDFViewerApplication.open(file);` with `PDFViewerApplication.open(file, { withCredentials: true });`
* [KIS-1640](https://jira.tie.ch/browse/KIS-1640): in [app.js](./web/app.js) initialize replace
  ```
  if (
    this.isViewerEmbedded &&
      AppOptions.get("externalLinkTarget") === LinkTarget.NONE
    ) {
      // Prevent external links from "replacing" the viewer,
      // when it's embedded in e.g. an <iframe> or an <object>.
      AppOptions.set("externalLinkTarget", LinkTarget.TOP);
    }
  )
  ```
  with 
  ```
  AppOptions.set("externalLinkTarget", LinkTarget.BLANK);
  ```
  External links must always open in new tab (also in webview where isEmbedded is false). It isn't enough to just set the default option.
* [KIS-1567](https://jira.tie.ch/browse/KIS-1567): 
  * in [pdf_sidebar.js](./web/pdf_sidebar.js) setInitialView set forceOpen to false `this._switchView(view, /* forceOpen */ false)`
  * add `/* TIE Styles */` section at the end of [viewer.css](./web/viewer.css)
  
# Testing
Quick test can be done locally with `gulp server` and then open `http://localhost:8888/web/viewer.html`
To test on wlswin build the project `gulp generic-legacy` and replace the contents of node_modules/tie-pdf.js tsclient/browser.
