# WordPress.org Replace Strings action
A helpful GitHub action that will handle replacing version numbers across plugin files.

## How it works

You must provide at least a `plugin_version`, `tested_wp_version`, `minimum_wp_version`, or `minimum_php_version` in order for this action to do anything.

It will automatically replace the corresponding value in your plugin file, readme.txt, and package.json (if you use one for your project).

The underlying logic of the action uses the corresponding NPM package scripts here: https://github.com/sc0ttkclark/wporg-replace-helper -- This may be useful for your project if you want to run the replacements locally instead of through a GitHub action.

## Running the action

1. Go to your GitHub repository
2. Go to Actions
3. Choose {Your action name}
4. Press "Run workflow"
5. Fill out the version number(s) you want to change
6. Hit submit

The version number(s) will be changed on any file(s) supported but that's only within the action run itself. You will still need the ability to commit the files changed (see Example action below).

<img width="370" alt="Screenshot of the 'Run workflow' dialog and the options shown" src="https://github.com/sc0ttkclark/wporg-replace/assets/709662/dfb3985b-ca01-4665-861f-2d8abe3b919a">

## Available options

### Required options

* `plugin_file`: The plugin file name like - `your-plugin.php`
* `plugin_path`: The plugin path which should in most cases be - `${{ github.workspace }}`

### At least one of these options must be provided

* `plugin_version`: The plugin version.
* `tested_wp_version`: The Tested up to WP version.
* `minimum_wp_version`: The Minimum WP version.
* `minimum_php_version`: The Minimum PHP version.

### Additional options

* `plugin_version_constant_name`: The plugin constant name (if applicable).
* `tested_wp_version_constant_name`: The Tested up to WP version constant name (if applicable).
* `minimum_wp_version_constant_name`: The Minimum WP version constant name (if applicable).
* `minimum_php_version_constant_name`: The Minimum PHP version constant name (if applicable).

## Example step

```yml
      - name: Run wporg-replace
        uses: sc0ttkclark/wporg-replace@v1.0.6
        with:
          plugin_file: ${{ env.WPORG_PLUGIN_FILE }}
          plugin_path: ${{ github.workspace }}
          plugin_version: ${{ github.event.inputs.plugin_version }}
          tested_wp_version: ${{ github.event.inputs.tested_wp_version }}
          minimum_wp_version: ${{ github.event.inputs.minimum_wp_version }}
          minimum_php_version: ${{ github.event.inputs.minimum_php_version }}
```

## Example simple action

This action allows inputs for workflow dispatch so that it can be manually run. After the strings are replaced, an auto-commit step will ensure those changes are comitted back to the branch chosen.

```yml
name: WordPress.org Replace Strings
env:
  WPORG_PLUGIN_FILE: 'your-plugin.php'
on:
  workflow_dispatch:
    inputs:
      plugin_version:
        description: 'Plugin version'
        required: false
      tested_wp_version:
        description: 'Tested up to WP version'
        required: false
      minimum_wp_version:
        description: 'Minimum WP version'
        required: false
      minimum_php_version:
        description: 'Minimum PHP version'
        required: false
jobs:
  wporg_replace:
    runs-on: ubuntu-latest
    steps:
      - name: What are we doing?
        run: |
          echo plugin_version: ${{ github.event.inputs.plugin_version }}
          echo tested_wp_version: ${{ github.event.inputs.tested_wp_version }}
          echo minimum_wp_version: ${{ github.event.inputs.minimum_wp_version }}
          echo minimum_php_version: ${{ github.event.inputs.minimum_php_version }}
      - name: Checkout the code
        uses: actions/checkout@v4
      - name: Run wporg-replace
        uses: sc0ttkclark/wporg-replace@v1.0.6
        with:
          plugin_file: ${{ env.WPORG_PLUGIN_FILE }}
          plugin_path: ${{ github.workspace }}
          plugin_version: ${{ github.event.inputs.plugin_version }}
          tested_wp_version: ${{ github.event.inputs.tested_wp_version }}
          minimum_wp_version: ${{ github.event.inputs.minimum_wp_version }}
          minimum_php_version: ${{ github.event.inputs.minimum_php_version }}
      - name: Commit changes
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          file_pattern: ${{ env.WPORG_PLUGIN_FILE }} readme.txt package.json
          commit_message: Update wporg version(s)
      - name: "Run if changes have been detected"
        if: steps.auto-commit-action.outputs.changes_detected == 'true'
        run: echo "Changes!"
      - name: "Run if no changes have been detected"
        if: steps.auto-commit-action.outputs.changes_detected == 'false'
        run: echo "No Changes!"
```

## Example full action

This example shows ALL of the options utilized, but you may only need to provide the plugin file name on your project.

```yml
name: WordPress.org Replace Strings
env:
  WPORG_PLUGIN_FILE: 'your-plugin.php'
  WPORG_PLUGIN_VERSION_CONSTANT_NAME: 'YOUR_PLUGIN_VERSION'
  WPORG_MINIMUM_WP_VERSION_CONSTANT_NAME: 'YOUR_PLUGIN_MINIMUM_WP_VERSION'
  WPORG_MINIMUM_PHP_VERSION_CONSTANT_NAME: 'YOUR_PLUGIN_MINIMUM_PHP_VERSION'
  WPORG_TESTED_WP_VERSION_CONSTANT_NAME: 'YOUR_PLUGIN_TESTED_WP_VERSION'
on:
  workflow_dispatch:
    inputs:
      plugin_version:
        description: 'Plugin version'
        required: false
      tested_wp_version:
        description: 'Tested up to WP version'
        required: false
      minimum_wp_version:
        description: 'Minimum WP version'
        required: false
      minimum_php_version:
        description: 'Minimum PHP version'
        required: false
jobs:
  wporg_replace:
    runs-on: ubuntu-latest
    steps:
      - name: What are we doing?
        run: |
          echo plugin_version: ${{ github.event.inputs.plugin_version }}
          echo tested_wp_version: ${{ github.event.inputs.tested_wp_version }}
          echo minimum_wp_version: ${{ github.event.inputs.minimum_wp_version }}
          echo minimum_php_version: ${{ github.event.inputs.minimum_php_version }}
      - name: Checkout the code
        uses: actions/checkout@v4
      - name: Run wporg-replace
        uses: sc0ttkclark/wporg-replace@v1.0.6
        with:
          plugin_file: ${{ env.WPORG_PLUGIN_FILE }}
          plugin_path: ${{ github.workspace }}
          plugin_version: ${{ github.event.inputs.plugin_version }}
          tested_wp_version: ${{ github.event.inputs.tested_wp_version }}
          minimum_wp_version: ${{ github.event.inputs.minimum_wp_version }}
          minimum_php_version: ${{ github.event.inputs.minimum_php_version }}
          plugin_version_constant_name: ${{ env.WPORG_PLUGIN_VERSION_CONSTANT_NAME }}
          tested_wp_version_constant_name: ${{ env.WPORG_TESTED_WP_VERSION_CONSTANT_NAME }}
          minimum_wp_version_constant_name: ${{ env.WPORG_MINIMUM_WP_VERSION_CONSTANT_NAME }}
          minimum_php_version_constant_name: ${{ env.WPORG_MINIMUM_PHP_VERSION_CONSTANT_NAME }}
      - name: Commit changes
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          file_pattern: ${{ env.WPORG_PLUGIN_FILE }} readme.txt package.json
          commit_message: Update wporg version(s)
      - name: "Run if changes have been detected"
        if: steps.auto-commit-action.outputs.changes_detected == 'true'
        run: echo "Changes!"
      - name: "Run if no changes have been detected"
        if: steps.auto-commit-action.outputs.changes_detected == 'false'
        run: echo "No Changes!"
```
