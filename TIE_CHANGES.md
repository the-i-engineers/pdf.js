# TIE changes on the fork of pdf.js
* remove console.log, console.warn and console.error from
  * [app.js](./web/app.js) _initializeMetadata
  * [pdf_sidebar_resizer.js](./web/pdf_sidebar_resizer.js) constructor
  * [ui_utils.js](./web/ui_utils.js) scrollIntoView
  * [viewer.js](./examples/mobile-viewer/viewer.js) setTitleUsingMetadata
* add `if (!Object) { return; }` at the beginning in constructor of FontFaceObject in [font_loader.js](./src/display/font_loader.js)
* add `if (!Promise) { return; }` at the beginning in constructor of MessageHander in [message_handler.js](./src/shared/message_handler.js)
* add `if (!Promise) { return capability; }` at the beginning in constructor of MessageHander in [util.js](./src/shared/util.js)
* set printResolution in defaultOptions of [app_options.js](./web/app_options.js) to 600
* in [app.js](./web/app.js)
  * replace `const viewerOrigin = new URL(window.location.href).origin || "null";` with `const viewerOrigin = "null";`
  * replace `PDFViewerApplication.open(file);` with `PDFViewerApplication.open(file, { withCredentials: true });`
* in [pdf_sidebar.js](./web/pdf_sidebar.js) setInitialView set forceOpen to false `this._switchView(view, /* forceOpen */ false)`
* add `/* TIE Styles */` section at the end of [viewer.css](./web/viewer.css)

# Publish new artifacts to Nexus
* install gulp and execute `gulp generic-es5`
* bump version in package.json and copy package.json into build/generic-es5 (remove dependencies)
* login to nexus with `npm --registry="http://dev-repository.tie.local:8081/repository/npm-hosted/" login`
* move into build/generic-es5 and publish with `npm --registry="http://dev-repository.tie.local:8081/repository/npm-hosted/" publish`