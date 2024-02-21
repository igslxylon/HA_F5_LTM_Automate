# Project HA_LTMAutomate
## Overview
The program will perform below tasks
1. Read LTM Excel and Output to several Ansible Variables YML file.
   Input: An Excel file path in 1st parameters
   Output: YML Files under Output Folder, default `REL_PATH_OUTPUT_DIR = './output'`
2. Commit the Output YML Files for related `<tsr_no>` to Github
3. Commit related ssl cert file to Github if necessary
## Python Version
`3.12`
## Tested Python Package Version
```
Package            Version
------------------ ------------
numpy              1.26.2
openpyxl           3.1.2
pandas             2.1.4
pip                23.3.2
PyGithub           2.1.1
PyJWT              2.8.0
python-dateutil    2.8.2
PyYAML             6.0.1
requests           2.31.0
ruamel.yaml        0.18.5
ruamel.yaml.clib   0.2.8
six                1.16.0
urllib3            2.1.0
```
## Quick Start
1. Clone the project .zip file and extract to a folder, `{project_home}`
2. Install python version `3.12` and Install the python module
```
pip install openpyxl
pip install pandas
pip install pyyaml
pip install PyGithub
pip install ruamel.yaml
```
3. Select or Setup and init a Github repository, `{GITHUB_REPOSITORY_NAME}` and create a Branch, `{GITHUB_BRANCH_NAME}`
4. Generate a Github token, `{GITHUB_TOKEN}`
5. Revise paramters in `config/LTMConfig.py`, replace the {GITHUB_REPOSITORY_NAME}, {GITHUB_TOKEN}
```
REL_PATH_ServiceDCMapping = 'config/Service_DC_Mapping.csv'
REL_PATH_OUTPUT_DIR = './output'
REL_PATH_OUTPUT_AUDIT_FILE = 'LTM_Audit.csv'
REL_PATH_SSL_CERT_DIR = './ssl_cert'
GITHUB_REPOSITORY_NAME = '{RepositoryName}'
GITHUB_TOKEN = '{GITHUB_TOKEN}'
GITHUB_BASE_URL = 'https://hagithub.home/api/v3'
...
GITHUB_BRANCH_NAME = {GITHUB_BRANCH_NAME}
SSL_UPLOAD_GITHUB = False
...
```
6. Execute Script
```
cd {project_home}
python .\LTMTaskAutomate.py ltm_highport_nonprd.xlsx
```
Sample Result
```
> python .\LTMTaskAutomate.py ltm_highport_nonprd.xlsx
2023-12-30 07:09:23 - root - INFO - Processing file: F:\My Documents\HA\F5Ansible\Dev\HA_LTMAutomate\ltm_highport_nonprd.xlsx
2023-12-30 07:09:23 - root - INFO - Completed Manupulate Fields for record: 1, excel row: 0
2023-12-30 07:09:23 - root - INFO - Completed Manupulate Fields for record: 1, excel row: 1
...
2023-12-30 07:09:23 - root - INFO - Preparing YML Template ...
2023-12-30 07:09:23 - root - INFO - Generating yml files for record 0 ...
2023-12-30 07:09:24 - root - INFO - Generating yml files for record 5 ...
2023-12-30 07:09:24 - root - INFO - Skip Generating yml files for record 14 ...
2023-12-30 07:09:24 - root - INFO - Start Commit files ... ['./output/tst/RID-00005/dc7-cloudnp-ltm01/eai-cdi-integrationsuite-aat.yml', './output/tst/RID-00006/dc7-cloudnp-ltm01/eai-cdi-integrationsuite-aat2.yml']       
2023-12-30 07:09:32 - root - INFO - Commit created successfully! id: ae04930aed4e9e85eacd8a8b3c98f6be6e7d2b5c
2023-12-30 07:09:32 - root - INFO - Start Commit files ... ['LTM_Audit.csv']
2023-12-30 07:09:39 - root - INFO - Commit created successfully! id: 43d4ea5be4c98d8ef4f3ed1a7527884fa52d9b34
```
## Folder Structure
```
.
├── config                                      # Config Folder
    ├── LTMConfig.py                            # File, Config Variables stored for Input, Output and Github parameters
    ├── logging.conf                            # File, Logging Conf File
    ├── LTM_HighPort_Domain_VIP_Mapping.csv     # File, CSV storing the Domain, VIP Info for LTM High Port
    └── ...         
├── output                                      # Destination Folder for Output Ansible variables yml files
    └── <env>                                   # List of Folders, naming with <env>, <tst|prd>
        └── <tsr_no>                            # List of Folders, naming with <tsr_no>, e.g. 'dc6-cloud-ltm01', 'dc7-cloudnp-ltm01'
            └── <device_name>                   # List of Folders, naming with <device_name>, e.g. 'RID-00001', 'RID-00002'
            └── <service_type>+<vs_name>.yml    # Output YML file, with pattern <vs_name>.yml, e.g. 'highport+vs_OCP4_eai-cdi-integrationsuite-aat3_80_dc6.yml', 'https+vs_OCP4_cloudAppRouter_443_dc7.yml'
├── service                                     # Service Folder for Service module storing Ansible Variable Patterns
├── template                                    # Template Folder for YML Template of Ansible Variable
├── util                                        # Folder for Python module, util
├── ltm_highport_nonprd.xlsx                    # Import Template File, The LTM Excel File for highport on nonprd, provided by User
├── <anyname>_<https|highport>_<tst|prd>.xlsx   # Import Excel, with naming including <https|highport>,env <tst|prd>
├── LTMTaskAutomate.py                          # File, Main Python File to start the program
├── app.log                                     # Log File
├── LTM_Audit_nonprd_highport.csv               # Audit CSV for LTM Highport non-production
├── LTM_Audit_nonprd_https.csv                  # Audit CSV for LTM HTTPS non-production
├── LTM_Audit_prd_highport.csv                  # Audit CSV for LTM Highport roduction
├── LTM_Audit_prd_https.csv                     # Audit CSV for LTM HTTPS production
└── README.md
```

## Output File
Output File is default set to under `./output`. Could set `REL_PATH_OUTPUT_DIR`
### Example
For a LTM record with below variables
```
tsr_no = 'RID_00006'
service_type = 'https'
vs_name = 'vs_OCP4_cloudAppRouter_443_dc6'
device_name = 'dc6-cloud-ltm01'

tsr_no = 'RID_00007'
service_type = 'highport'
proj = 'eai-cdi-integrationsuite-aat'
device_name = 'dc7-cloudnp-ltm01'
```
The Output Files with default are
```
./output/prd/RID_00009/dc6-cloud-ltm01/highport+vs_OCP4_eai-cdi-integrationsuite-aat3_80_dc6.yml
./output/tst/RID_00006/dc7-cloudnp-ltm01/https+vs_OCP4_cloudAppRouter_443_dc7.yml
```


## LTM Excel File
To be input by User

Accepted Values in `Service Type` in the Excel
- High Port
- HTTPS

