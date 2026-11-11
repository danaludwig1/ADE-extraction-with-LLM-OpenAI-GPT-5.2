# ADE-extraction-with-LLM-OpenAI-GPT-5.2
This project contains the python source code for using the large language model "GPT-5.2" for extracting Adverse Drug Events from a set of clinical notes. The source code and configuration is designed to be as identical as possible to the project "ADE-extraction-with-LLM-OpenAI-o1", and has only been changed when necessary to function correctly with the model "GPT-5.2".  See the "README.md" for the project "ADE-extraction-with-LLM-OpenAI-o1" for further details.

This sample code is close to the sample code used in the journal paper
with the following exceptions:

* The journal paper used OpenAI o1, but this sample code uses GPT-5.2.
  This is because we expect that "o1" will not be available soon.
* The journal paper uses the "completions" API, and this sample code
  uses the "responses" API. This is because OpenAI states that the
  responses API is newer and can give a bit better performance
* The journal paper gets json output through parameter
    response_format={ "type":"json_object"}
  This sample code uses the parameters:
```
    text={
      "format": {
         "type": "json_schema",
         "name": "notes",
         "strict":True,
         "schema": json_schema_one (or json_schema_two)
       }
     }
```
  Where "json_schema_one" and "json_schema_two" are provide to you
  in the two files "json_schema_one.json" and "json_schema_two.json"
  This was done because GPT-5.2 "responses" API does not accept the
  previous parameters for json output.
* This sample code supports saving the output of the reasoning summaries,
  if any, in the "*_out.txt" output text files. The reassoning is
  interesting and may be helpful in future improvements to model.
* This sample code generates a slightly different output JSON format 
  than the code published in the paper, and shown in the dataset json files.
  - The paper and files have a format:
```
    [
      {
        "patientepicid": "PPPPP",
        "died_note_key": "AAAAA",
        "Adverse Event 1": {
           "Adverse Event": "aaaaaaa",
           "Medication": "mmmmmmmm",
           ... etc.
        },
        "Adverse Event 2": {
           ... etc.
        }
      }
    ]
```
  - This code generates files with a format:
```
    {
        "patientepicid": "PPPPP",
        "died_note_key": "AAAAA",
        "ADEs": [
           {
           "ade_seq": 1,
           "Adverse Event": "aaaaaaa",
           "Medication": "mmmmmmmm",
           ... etc.
           },
           {
           "ade_seq": 2,
           ... etc.
           }
        ]
    }
```
  The reasons for this code change were:
  - The original code could not be described in a json schema due to the
    variable and unlimited number of field names: 
       "Adverse Event 1", "Adverse Event 2", etc.
  - The OpenAI structured data rules required that the schema be a "dictionary"
    and not a "list", at the top level.

## Software Files Description
The software includes the following files:

* __two_pass_52_2025-12-15.py__. - This is the python script for implementing the two-pass large language model for extracting adverse events from clinical notes.  The text of the first and second prompts are included in the file. This implementation has been tested to work on the OpenAI service on model "GPT-5.2".

* __json_schema_one.json__, __json_schema_two.json__ - These are json schema files referenced in the script, and used to define the output format of the models

* __sample_dot_env_file.txt__ - this shows the format of a required ".env" file. You will need to replace the fake values with your own values (such as OPENAI_API_KEY and file pathnames).

## Usage Notes
Use the following command to extract ADEs from a set of notes:

```
python3 two_pass_52_2025-12-15.py
```
