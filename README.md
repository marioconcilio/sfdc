# Salesforce DX Sample App

This repository contains the source of an example SFDX project.

## Resources

For details on using SFDX, please visit [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev).

## Description of Files and Directories

* **sfdx-project.json**: Required by Salesforce DX. Configures your project. Use this file to specify the parameters that affect your Salesforce development project.
* **config/project-scratch-def.json**: Sample file that shows how to define the shape of a scratch org. You reference this file when you create your scratch org with the `sfdx force:org:create` command.
* **force-app**: Directory that contains the source for the sample Force.com app and tests.
* **.forceignore**: Optional SFDX file. Specifies intentionally untracked files that you want SFDX to ignore.
* **.gitignore**: Optional Git file. Specifies intentionally untracked files that you want Git (or in this case GitHub) to ignore.