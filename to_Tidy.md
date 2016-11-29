## Step 3 : to_Tidy

<p align="center">
  <img src="INBO_AF_03_to_Tidy.png">
</p>

If the dataset is approved by the structural validation, the script for the transformation to the DynamoDB table "Tidy" is automatically called upon.
This phase consists of the following components :

- Read the dataset from the DynamoDB table "Raw"
- Create a csv export of the dataset in S3 folder "RawExports"
- Retrieve the partner-specific R transformation script and YAML file with the business rules from GitHub
- Transform the dataset, based on the R-script and YAML file
- Write the result to the DynamoDB table "Tidy"
- Generate a Content Report, which contains the result of the content validation of the dataset
- Move the source file from the S3 folder "Raw" to "Transformed" with a link to the Technical and Content Report and a download link to the corresponding csv RawExports

What is shown in the **Content Report** ?

- Name of the source file
- Direct link to the R-script and YAML file on GitHub
- The result of the content validation, with a subset of unique values per column and NaNs
- Relevant plots, based on the data type (leaflet with observations on a map, histogram, ...)
- Three action buttons for the INBO researcher to make a decision, based on the report :
 - **Accept** the result
 - **Refuse** the result
 - Adapt the R-script and YAML file and **re-run** the transformation from DynamoDB table "Raw" to "Tidy" to obtain a better result

The Content Report is generated with the following name convention : `{ProviderID}_{filename}_{Date}_{Time}_CONTENT.html`. The content and name convention of the report facilitates the **versioning**, because the INBO researcher will always know with which file the transformation was done, using which R and YAML file and at what date/time.

Because the reports are never deleted by the system, the check is built in that verifies if the source file exists in the S3 folder "Transformed", when a content report is opened. If no file is found, the action buttons will not be displayed, because these are not applicable to the current sitation.

In the figure below the different scenarios of this phase are shown in different flows. In this scheme there is a clear distinction between movements of the dataset in the DynamoDB tables and movement of the source file in the S3 folders, to make it as clear as possible for the end user.

<p align="center">
  <img src="INBO_to_Tidy.png">
</p>

**Scenario 1** : INBO researcher **approves** the content validation
> - Dataset is approved and remains in the DynamoDB table "Tidy"
- Source file is moved from S3 folder "Transformed" to "Validated"

**Scenario 2** : INBO researcher completely **refuses** the content validation
> - Dataset is  removed from DynamoDB table "Raw"
- Dataset is removed from DynamoDB table "Tidy"
- Source file is moved from S3 folder "Transformed" to "Refused"
- CSV export from the dataset in DynamoDB table "Raw" is deleted from S3 folder "RawExports"

**Scenario 3** : INBO researcher notices that only minors adjustments are necessary to correct the transformation and chooses to **re-run** the transformation
> - Dataset is removed from DynamoDB tabel "Tidy"
- INBO researcher changes the R-script and/or YAML file with business rules and commits to GitHub
- INBO researcher presses the "Re-run" action button in the content report
- The new R and YAML files are retrieved from GitHub and the flow from DynamoDB table "Raw" to "Tidy" is run again
- This creates a new Content Report
- Source file is not moved and remains in S3 folder "Transformed"

Because the to_Tidy transformation is automatically performed when the dataset is approved by the structural validation, we decided to build in a fourth scenario as a safety net.

**Scenario 4** : INBO researcher encounters an error in the to_Tidy transformation
>The researcher is shown a default Content Report, which contains :
- Name of the source file
- Link to the partner-specific YAML file
- Three action buttons: "Approve", "Refuse", "Re-run"
