# Lettering Software

## Source

Automation: [github.com/aoswalt/lettering-automation](https://github.com/aoswalt/lettering-automation)

Reporting Tool: [github.com/aoswalt/reporting-tool](https://github.com/aoswalt/reporting-tool)

## Application Designs

### Shared

* C# applications
* Run SQL on the AS400
* Authentication through Kerberos

### Reporting Tool

* Pulls lettering order information from the AS400
* Export options
* Safety limit checkbox to prevent accidental large queries
* Can save and load reports
* Ctrl-Clicking "Run Query" puts query in query tab instead of running
* 3 Components
  * Orders
    * Specific for lettering orders
    * Preset filters for specific lettering types
    * Entry fields for more filtered queries as needed
  * Query
    * Designed for manual queries
    * Can add prompts with `?`s
    * Can add prompt labels separated by `,`s
  * Custom
    * GUI for building custom queries
    * Currently only supports `AND`ing where clauses

### Automation

* Only cut lettering is automated but can report on sew and stones
* With no date specified, pulls orders entered on previous work day
  * Previous day skips through weekends
  * Holidays are skipped through as with weekends
  * Holidays are specified in `holidays.txt`
* Configuration is loaded from `config.json`
  * Configuration can be edited through the `Edit Configs` menu option
* Local fonts are compared to network fonts, listing any that need to be installed/updated
* Local macro automation library is compared to network version, updating if out of date
* For each order, the filesystem is first checked for the built artwork file
* For unbuilt orders, the appropriate template is opened with Corel
  * An invisible shape is created with the order data attached
  * A layer named "Automate" is created to trigger the template's macro
  * The macro reads the data from the hidden shape and calls the build function
  * The artwork is built and moved to a new page
  * On "Next"
    * Artwork is saved to the desktop
    * Artwork is exported if needed
    * Open files are closed
    * The next order is processed
* On completion
  * A report is generated of all orders found, built or not
  * User moves the built orders to the network drive
    * Built orders are saved locally to prevent accidental overwrite risk

## Macros / Templates

* Each template is saved with its own macro
  * For automation, listens for a layer named "Automate" to be created
  * Can be manually run through its dialog box
* Automation library
  * Depended on by all macros
  * Created to provide a shared functions between templates
  * Location based on Corel version because user-dependant otherwise
* Elements on templates are named
  * With Corel's object manager sidebar, shapes and layers can be named
  * Shapes are named to be accessible from the macros
  * Layers are generally used and named for organization
    * A "Layer 1" **MUST** exist for macros to place in-progress artwork to keep template clean
    * An "Art" layer holds all of the elements used for building
    * An "Info" layer holds all of the elements not used for building and is locked
