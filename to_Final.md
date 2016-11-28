## Step 4 : to_Final

<p align="center">
  <img src="INBO_AF_04_to_Final.png">
</p>

In the tab "Final" the end user will have access to all the functionalities that are related to the DynamoDB table "Final".

When the user start the to_Final transformation, the following steps are performed in the background :
- Clear DynamoDB table "Final"
- Content of S3 folder "Validated" is retrieved
- to_Final transformation is performed, solely on the datasets of the files that are in the S3 folder "Validated"
- Name matching with the INBO tsv
- GBIF columns are added to the dataset
- Duplicates are flagged
- Final report is generated with as name convention : `Final-Reports_{Date}_{Time}_FINAL.hmtl`

Apart from the transformation, the user also has the option to generate a csv export of the content of the current DynamoDB table "Final".
Both the reports and the exports are shown in 2 separate lists in this tab.

In this phase, we built in an extra scenario as a safety net as well.

**Scenario** : Dataset passed structural and content validation. INBO researcher finds issues after performing to_Final transformation.
> - Go to Validated tab
- Move source file from S3 folder "Validated" to "Transformed"
- Change R-script and/or YAML file on GitHub
- Re-run to_Tidy transformation
- Re-run to_Final transformation

This way you can still make adjustments to the dataset and go one step back in the workflow to make minor adjustments, without having to start from scratch.
