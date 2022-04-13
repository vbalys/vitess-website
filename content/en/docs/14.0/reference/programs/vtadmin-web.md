---
title: vtadmin-web
description: The VTAdmin web interface
---

## vtadmin-web Build Vars 

These environment variables are also referenced in [react-app-env.d.ts](https://github.com/vitessio/vitess/blob/main/web/vtadmin/src/react-app-env.d.ts).

| Name | | Definition |
| -------- | --------- | --------- |
| `REACT_APP_VTADMIN_API_ADDRESS` | **Required** | The full address of `vtadmin-api`'s HTTP interface. Example: "http://127.0.0.1:12345" |
| `REACT_APP_BUGSNAG_API_KEY` | Optional | An API key for https://bugsnag.com, for those using Bugsnag to capture client-side errors. If defined, the [@bugsnag/js](https://www.npmjs.com/package/@bugsnag/js) client will be initialized. Your Bugsnag API key can be found in your Bugsnag Project Settings. | 
| `REACT_APP_BUILD_BRANCH` | Optional | For debugging purposes. The branch that was used to build `vtadmin-web`. If defined, this is displayed on the Debug page. When `REACT_APP_BUGSNAG_API_KEY` is also defined, Bugsnag errors will be annotated with the branch. | 
| `REACT_APP_BUILD_SHA` | Optional | For debugging purposes. The SHA that was used to build `vtadmin-web`. If defined, this is displayed on the Debug page. When `REACT_APP_BUGSNAG_API_KEY` is also defined, Bugsnag errors will be annotated with the SHA.  | 
| `REACT_APP_DOCUMENT_TITLE` | Optional | Used for the document.title property. The default is "VTAdmin". Overriding this can be useful to differentiate between multiple VTAdmin deployments, e.g., "VTAdmin (staging)". |
| `REACT_APP_ENABLE_EXPERIMENTAL_TABLET_DEBUG_VARS` | Optional | Recommended. Defaults to "false". When "true", enables front-end components that query vtadmin-api's /api/experimental/tablet/{tablet}/debug/vars endpoint.| 
| `REACT_APP_FETCH_CREDENTIALS` | Optional | Configures the `credentials` property for fetch requests made against vtadmin-api. If unspecified, uses fetch defaults. See https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#sending_a_request_with_credentials_included | 
| `REACT_APP_READONLY_MODE` | Optional | Defaults to "false". If "true", UI controls that correspond to write actions (PUT, POST, DELETE) will be hidden. Note that this *only* affects the UI. If write actions are a concern, Vitess operators are encouraged to also configure vtadmin-api for role-based access control (RBAC) if needed; see https://github.com/vitessio/vitess/blob/main/go/vt/vtadmin/rbac/rbac.go | 

