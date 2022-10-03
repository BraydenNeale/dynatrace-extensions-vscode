![illustration](previews/proj-illustration.png)

# Dynatrace Extensions Copilot

This is a VisualStudio Code Extension that aims to provide support for all aspects of developing Dynatrace Extensions 2.0. It provides a specialised toolkit to facilitate extension & environment management, lifecycle operation automation, content validation and automation, and more. Browse through the contents below, or jump straight to [using it](#start-using-it).

- [Dynatrace Extensions Copilot](#dynatrace-extensions-copilot)
  - [Settings](#settings)
  - [Workflows (Commands)](#workflows-commands)
    - [🚀 Initialize workspace](#-initialize-workspace)
    - [📄 Load schemas](#-load-schemas)
    - [🔐 Generate certificates](#-generate-certificates)
    - [🔗 Upload certificate](#-upload-certificate)
    - [🎁 Build extension](#-build-extension)
    - [📤 Upload extension to tenant](#-upload-extension-to-tenant)
    - [🔂 Activate extension on tenant](#-activate-extension-on-tenant)
    - [📑 Create documentation](#-create-documentation)
  - [Assisted extension development](#assisted-extension-development)
    - [🎹 Code completions](#-code-completions)
    - [💡 Code actions](#-code-actions)
    - [🔎 Code lens](#-code-lens)
  - [Custom Dynatrace View](#custom-dynatrace-view)
    - [📂 Extension 2.0 Workspaces](#-extension-20-workspaces)
    - [⚡ Dynatrace Environments](#-dynatrace-environments)
- [Start using it!](#start-using-it)

## Settings

This extension can leverage the following VSCode settings (global or workspace level):

Settings controlling the extension's behavior:

- `dynatrace.metricSelectorsCodeLens` - Controls whether Code Lenses are enabled for metric selectors
- `dynatrace.entitySelectorsCodeLens` - Controls whether Code Lenses are enabled for entity selectors

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

![commands](previews/commands.gif)

### 🚀 Initialize workspace

Initializes a new workspace for Dynatrace Extension (2.0) Development.
This includes loading up schemas, creating extension folder and stub, and creating an empty dist folder.
This will also configure the `yaml.schema` extension to validate `extension.yaml` with your chosen schema version.
Once finished, the workspace appears in the new Dynatrace view.

### 📄 Load schemas

Downloads schema files of a specific version and updates the `yaml.schema` extension to validate `extension.yaml` with this version.
If repository has an `extension.yaml` file, it updates the version in there too.

### 🔐 Generate certificates

Generates all the required keys and certificates needed for signing and validating Extensions 2.0.
At the end, you can upload your newly generated root certificate to the Dynatrace Credentials Vault.

### 🔗 Upload certificate

Uploads the workspace's root (CA) certificate to the Dynatrace Credentials Vault.

### 🎁 Build extension

Builds your extension and its artefacts into a signed .zip archive which is placed inside the `dist` folder of the workspace.
Building extensions is only supported for custom extensions (i.e. name must start with `custom:`).
Upon successful build, you may also upload this package to Dynatrace.

![build_command](previews/build_command.gif)

### 📤 Upload extension to tenant

Uploads the most recent package from your workspace's `dist` folder to the currently connected Dynatrace tenant.
After upload, you are also prompted to activate this latest version. In the case that you reached the maximum allowed
number of extension versions, you will be prompted to delete the oldest one.

### 🔂 Activate extension on tenant

Activates a version of your workspace's extension on the currently connected Dynatrace tenant. The tenant is polled for all
available versions of the extension and you are prompted to choose which one to activate.

### 📑 Create documentation

Reads through the `extension.yaml` file and creates a `README.md` file next to the `extension` folder,
documenting (as best as possible) the extension package and its contents.

## Assisted extension development

### 🎹 Code completions

The extension will trigger suggestions for code completion where possible. If the trigger doesn't happen automatically,
use `Ctrl + Space` to trigger it manually.

**Note**: Suggestions from other extensions (such as YAML Schema) may take precedence over this extension.

Currently implemented completions trigger:

- on `fromType` and `toType` attributes of any `topology.relationships` item
- on `sourceAttribute` and `destinationAttribute` attributes of any relationship based on entity mapping rules (provided you have already filled in `fromType` and `toType` respectively)
- on `entityType` and `entityTypes` anywhere in the yaml, relevant entity types are suggested
- on lists of attribute-type properties, on `key` the keys of relevant entity attributes are suggested
- on `entitySelectorTemplate` can make use of `Ctrl + Space` to trigger completions as the selector is being built
- on `iconPattern` (within `topology.rules`) or `icon` (within `staticContent.header`) - you can browse available Barista Icon codes
- on `key` for card keys either inside `layout.cards` or individual card type lists - card keys that have not been used yet are suggested

![intellisense](previews/intellisense.gif)

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

![auto_charts](previews/pro_chart_building.gif)

### 🔎 Code lens

Code Lens are actionable, contextual information, interspersed with your code.
For Dynatrace Extensions, these can help trigger some code-related actions to your connected tenant.

**Metric Selector Code Lenses**

- See the last validation status of any metric selector
- Validate any metric selector against a connected Dynatrace tenant
- Run any metric selector and visualize the query results in a separate editor panel

![metric_codelens](previews/metric_code_lens.gif)

**Entity Selector Code Lenses**

- See the last validation status of any entity selector
- Validate any entity selector against a connected Dynatrace tenant
- Run any entity selector and visualize the query results in the Dynatrace output panel

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

Currently, this project is in alpha stage - this means there is heavy development still happening, things may break, or change completely between versions.
Bugs are expected, however, during this stage please provide any feedback to radu.stefan@dynatrace.com regarding:

- full feature ideas
- workflow ideas
- ease of use of current features
- usefulness (or lack of) for current features

Right now, the extension is not published to the marketplace so you'll have to install it from the `.vsix` file packaged in every [release](https://github.com/dynatrace-extensions/dynatrace-extension-developer/releases).
In VSCode, open the Extensions menu, click the "..." and choose "Install from VSIX..".

Once installed, why not initialize a new workspace, or connect a tenant? Head over to the new [Dynatrace view](#custom-dynatrace-view) to start.