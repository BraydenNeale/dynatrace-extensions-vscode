![illustration](previews/proj-illustration.png)

# Dynatrace Extensions Copilot

This is a VisualStudio Code Extension that aims to provide support for all aspects of developing Dynatrace Extensions 2.0. It provides a specialised toolkit to facilitate extension & environment management, lifecycle operation automation, content validation and automation, and more. Browse through the contents below, or jump straight to [using it](#start-using-it).

- [Dynatrace Extensions Copilot](#dynatrace-extensions-copilot)
  - [Settings](#settings)
  - [Workflows (Commands)](#workflows-commands)
    - [🚀 Initialize workspace](#-initialize-workspace)
    - [📄 Load schemas](#-load-schemas)
    - [🔐 Generate certificates](#-generate-certificates)
    - [🔗 Distribute certificate](#-distribute-certificate)
    - [🎁 Build extension](#-build-extension)
    - [📤 Upload extension to tenant](#-upload-extension-to-tenant)
    - [🔂 Activate extension on tenant](#-activate-extension-on-tenant)
    - [📑 Create documentation](#-create-documentation)
    - [📈 Create overview dashboard](#-create-overview-dashboard)
    - [🔥 Fast Development Mode](#-fast-development-mode)
  - [Assisted extension development](#assisted-extension-development)
    - [🎹 Code completions](#-code-completions)
    - [💡 Code actions](#-code-actions)
    - [🔎 Code lens](#-code-lens)
  - [Custom Dynatrace View](#custom-dynatrace-view)
    - [📂 Extension 2.0 Workspaces](#-extension-20-workspaces)
    - [⚡ Dynatrace Environments](#-dynatrace-environments)
- [Start using it!](#start-using-it)
    - [Current project stage](#current-project-stage)
    - [Installation steps](#installation-steps)

## Settings

This extension can leverage the following VSCode settings (global or workspace level):

Settings controlling the extension's behavior:

- `dynatrace.metricSelectorsCodeLens` - Controls whether Code Lenses are enabled for metric selectors
- `dynatrace.entitySelectorsCodeLens` - Controls whether Code Lenses are enabled for entity selectors
- `dynatrace.fastDevelopmentMode` - Controls whether [Fast Development Mode](#-fast-development-mode) is enabled

Settings for using your own credentials:

- `dynatrace.developerKeyLocation` - File path. Bring your own developer key instead of generating a new one.
- `dynatrace.developerCertificateLocation` - File path. Bring your own developer certificate instead of generating a new one.
- `dynatrace.rootOrCaCertificateLocation` - File path. Bring your own root (CA) certificate instead of generating a new one.

Settings for generating new certificates:

- `dynatrace.certificateCommonName` - When generating new certificates, specifies the common name (CN) attribute of the certificate. Defaults to "Extension Developer".
- `dynatrace.certificateOrganization` - When generating new certificates, specifies the organization (O) attribute of the certificate.
- `dynatrace.certificateOrganizationUnit` - When generating new certificates, specifies the organization unit (OU) attribute of the certificate.
- `dynatrace.certificateStateOrProvince` - When generating new certificates, specifies the state or province (ST) attribute of the certificate.
- `dynatrace.certificateCountryCode` - When generating new certificates, specifies the country code (C) attribute of the certificate.

## Workflows (Commands)

This extension exposes commands to support typical extension development workflows.
All commands are invoked from the command palette (`Ctrl + Shift + P`), and are prefixed with `Dynatrace`.

![commands](previews/command_palette.gif)

### 🚀 Initialize workspace

Initializes a new workspace for Dynatrace Extension (2.0) Development.
This includes loading up schemas, creating extension folder and stub, and creating an empty dist folder.
This will also configure the `yaml.schema` extension to validate `extension.yaml` with your chosen schema version.
Once finished, the workspace appears in the new Dynatrace view.

![initialize](previews/cmd_init.gif)

### 📄 Load schemas

Downloads schema files of a specific version and updates the `yaml.schema` extension to validate `extension.yaml` with this version.
If repository has an `extension.yaml` file, it updates the version in there too.

### 🔐 Generate certificates

Generates all the required keys and certificates needed for signing and validating Extensions 2.0.
At the end, you can distribute your newly generated root certificate to the Dynatrace Credentials Vault and locally installed
OneAgents or ActiveGates.

### 🔗 Distribute certificate

Uploads the workspace's root (CA) certificate to the Dynatrace Credentials Vault. The user is prompted whether to also upload
this certificate to any locally installed OneAgents or ActiveGates (if detected). Due to the default certificate locations this
follow-up requires administrator level permissions (example for Windows, Run As Administrator).

### 🎁 Build extension

Builds your extension and its artefacts into a signed .zip archive which is placed inside the `dist` folder of the workspace.
Building extensions is only supported for custom extensions (i.e. name must start with `custom:`).
Upon successful build, you may also upload this package to Dynatrace.

![build_command](previews/cmd_build.gif)

### 📤 Upload extension to tenant

Uploads the most recent package from your workspace's `dist` folder to the currently connected Dynatrace tenant.
After upload, you are also prompted to activate this latest version. In the case that you reached the maximum allowed
number of extension versions, you will be prompted to delete the oldest one.

![upload_ext_command](previews/upload_extension.gif)

### 🔂 Activate extension on tenant

Activates a version of your workspace's extension on the currently connected Dynatrace tenant. The tenant is polled for all
available versions of the extension and you are prompted to choose which one to activate.

![activate_ext_command](previews/activate_extension.gif)

### 📑 Create documentation

Reads through the `extension.yaml` file and creates a `README.md` file next to the `extension` folder,
documenting (as best as possible) the extension package and its contents.

### 📈 Create overview dashboard

Reads through the `extension.yaml` file and creates an overview (landing page) dashboard. This is placed in a folder called
`dashboards` inside the `extension` folder as `overview_dashboard.json`; the YAML is also modified to include this asset.
After generating it, you're prompted if you want to also upload it to your connected tenant. The dashboard contains links
to extension configuration as well as every entity defined within the YAML, as well as single value tiles displaying the 
count of each entity type, tables listing each entity and up to two graph charts for each entity type.

### 🔥 Fast Development Mode

**Fast Development Mode** is a workflow focused on saving as much time as possible and providing immediate feedback to
extension developers. When enabled, on every document Save your extension version is automatically incremented, packaged,
signed, uploaded to Dynatrace and activated. If too many versions are in the environment already, one is automatically
removed. Any errors are communicated instantly and your VS Code status bar has an indicator of the build status.

How does this help? Apart from eliminating time spent on manual operations you also get immediate updates to your
extension's static assets (topology model, screens, metadata, dashboards, and alerts) that you can verify in the
environment without having to go through a (slower) extension activation process.

## Assisted extension development

### 🎹 Code completions

The extension will trigger suggestions for code completion where possible. If the trigger doesn't happen automatically,
use `Ctrl + Space` to trigger it manually.

**Note**: Suggestions from other extensions (such as YAML Schema) may take precedence over this extension.

Currently implemented completions trigger:

- on `fromType:` and `toType:` attributes of any `topology.relationships` item
- on `sourceAttribute:` and `destinationAttribute:` attributes of any relationship based on entity mapping rules (provided you have already filled in `fromType` and `toType` respectively)
- on `entityType:` and `entityTypes:` anywhere in the yaml, relevant entity types are suggested
- on lists of attribute-type properties, on `key:` the keys of relevant entity attributes are suggested
- on `entitySelectorTemplate:` you can make use of `Ctrl + Space` to trigger completions as the selector is being built
- on `entitySelectorTemplate:` you can auto-complete selectors for relationships that are seen in the YAML
- on `iconPattern:` (within `topology.rules`) or `icon:` (within `staticContent.header`) - you can browse available Barista Icon codes
- on `key:` for card keys either inside `layout.cards` or individual card type lists - card keys that have not been used yet are suggested
- on `value:` for metrics and dimensions of a prometheus extension if data has been scraped already
- on `description:` in `metrics` section for those metrics that have been scraped from a Prometheus endpoint

![code completions](previews/code_completions.gif)

### 💡 Code actions

The extension will highlight actions to add snippets of code where possible.
On lines with specific keywords, the lightbulb (💡) icon will show up to list things that can be automatically added to the code.

Currently implemented action triggers:

- inside `propertiesCard` when clicking on `properties` - you can automatically add properties for the entity's attributes and relations
- inside `entitiesListCards` when clicking on `columns` - you can automatically add columns for the listed entity's attributes and relations
- inside `chartsCards` and `entitiesListCards` when clicking on `charts` inside a card - you can automatically add charts for metrics that aren't already in the card
- inside `graphChartConfig` when clicking on `metrics` - you can add additional metrics to your chart, that aren't already used within the surrounding card
- inside `screens` when clicking on `chartsCards` - you can automatically add chart cards for entire feature sets of metrics
- inside `screens` when clicking on `entitiesListCards` - you can automatically add cards for listing this entity as well as the related ones
- when clicking on `metrics` or `dimensons` within the `prometheus` data source - automatically add details from scraped Prometheus data
- on `screens` - you can automatically generate entire screens for your entities
- inside `entitiesListCards` when clicking on `filtering` - insert entire filtering blocks with a default filter by name
- inside `entitiesListCards` and inside `filtering`, when clicking on `filters` - insert individual filter for the entity's attributes
- inside `screens` when clicking on `actions` - insert global actions to configure the extension
- inside `actions` when clicking on `actions` - insert an action expression to configure the extension

![auto_charts](previews/pro_chart_building.gif)

### 🔎 Code lens

Code Lens are actionable, contextual information, interspersed with your code.
For Dynatrace Extensions, these can help trigger some code-related actions to your connected tenant or other external endpoints.

**Metric Selector Code Lenses**

- See the last validation status of any metric selector
- Validate any metric selector against a connected Dynatrace tenant
- Run any metric selector and visualize the query results in a separate editor panel

![metric_codelens](previews/metric_code_lens.gif)

**Entity Selector Code Lenses**

- See the last validation status of any entity selector
- Validate any entity selector against a connected Dynatrace tenant
- Run any entity selector and visualize the query results in the Dynatrace output panel

**Prometheus Code Lenses**

- Connect to a Prometheus endpoint and scrape metrics data. Scraped details can then be used:
  - To automatically insert metric definitions in the `prometheus` section of the YAML
  - To automatically insert dimensions in the `prometheus` section of the YAML
  - To automatically insert metric metadata in the `metrics` section of the YAML
- Get a timestamp of when details were last scraped.

**WMI Query Code Lenses**

- Run WMI queries against the local Windows machine where a WMI extension is being developed
  - To validate that the WMI query is valid
  - To browse the results of the query and check the column values
  - To get the number of unique dimensions that a query would create for this machine

**Unified Analysis Screens Code Lenses**

- Open the List or Details Unified Analysis screen for any entity type

## Custom Dynatrace View

Access the new Dynatrace view by clicking the Dynatrace logo from your main activity bar (by default, on the left).
This is your all-in-one view over extension workspaces and Dynatrace tenants; use it to boost your operational efficiency.

### 📂 Extension 2.0 Workspaces

Once you initialize a workspace, this becomes available in the Extensions 2.0 Workspaces section.
This section offers a high level overview of all registered workspaces and the extensions within them.

Buttons within this view enable quick actions for:

- Adding and initializing a new workspace (plus button at the top)
- Quickly opening one of the extension.yaml files (file button next to the extension name)
- Opening one of the extension workspaces (folder button next to the workspace name)
- Removing the

![workspaces_view](previews/extensions_view.png)

### ⚡ Dynatrace Environments

Use the Dynatrace Environments view to register all the Dynatrace (SaaS/Managed) environments that you work with.
This enables you to save connection details and quickly select which environment you're working with (for API-based operations).

Once environments are added, you get a view into all extensions available in that environment.

At a glance:

- See what extensions are deployed and what version is active
- See what monitoring configurations are configured and what is the latest state
- Quickly select which environment's details you want to use for API-based operations

You can edit or remove environment details from the same list.

![environments_view](previews/tenants_view.png)

# Start using it!

### Current project stage

Currently, this project is in beta stage - this means that most functionality present currently is locked in and should not receive any major changes unless absolutely necessary. Some bugs are still expected, however, the primary focus is catching and fixing them.

Feel free to create GitHub Issues and PRs for:

- any bug found, sharing steps to reproduce and the extension.yaml if appliable
- any bug fix you can provide
- suggestions for improvements to current workflows

### Prerequisites 

This VS Code extension uses a number of Dynatrace APIs to help make the process of creating a Dynatrace extension. Because of that, it requires an API Access token with certain scopes. Below are the current scopes that an Access Token requires : 

- `WriteConfig`
- `ReadConfig`
- `credentialVault.read`
- `credentialVault.write`
- `extensions.read`
- `extensions.write`
- `extensionEnvironment.write`
- `extensionEnvironment.read`
- `extensionConfigurations.read`
- `extensionConfigurations.write`
- `metrics.read`
- `entities.read`
- `settings.read`
- `settings.write`

In order to make it easier to create this token, you may also create an Access Token through the Dynatrace UI with this scope `apiTokens.write` ([documentation](https://www.dynatrace.com/support/help/shortlink/api-tokens-v2-api-post-token)), replace the TENANT-ID and Api-Token Value , and run the `curl` command below : 

```sh
curl -X POST "https://<TENANT-ID>.live.dynatrace.com/api/v2/apiTokens" -H "accept: application/json; charset=utf-8" -H "Content-Type: application/json; charset=utf-8" -d "{\"name\":\"Dynatrace Extensions Copilot\",\"scopes\":[\"entities.read\",\"extensionConfigurations.read\",\"extensionConfigurations.write\",\"extensionEnvironment.read\",\"extensionEnvironment.write\",\"extensions.read\",\"extensions.write\",\"metrics.read\",\"settings.read\",\"settings.write\",\"credentialVault.read\",\"credentialVault.write\",\"ReadConfig\",\"WriteConfig\"]}" -H "Authorization: Api-Token <CHANGE-ME>"
```

### Installation steps

Right now, the extension is not published to the marketplace so you'll have to install it from the `.vsix` file packaged in every [release](https://github.com/dynatrace-extensions/dynatrace-extension-developer/releases).
In VSCode, open the Extensions menu, click the "..." and choose "Install from VSIX..".

Once installed, why not initialize a new workspace, or connect a tenant? Head over to the new [Dynatrace view](#custom-dynatrace-view) to start.

![get_started](previews/get_started.gif)

# Known Limitations

- When clicking the `+` button to add a new workspace, if the opened folder is empty the workspace is not initialized straight away
  - Workaround: click the Dynatrace icon to trigger the workflow or use the command pallete to invoke the command
- Auto-completions and Code actions might fail on invalid YAML
  - Solution: correct your YAML first, then try again
- I just loaded schemas but validation isn't working
  - Solution: open command palette (`Ctrl+Shift+P`) then choose "Reload window"