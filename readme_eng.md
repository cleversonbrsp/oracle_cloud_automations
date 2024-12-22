# OCI AUTO TAG

### Author: Cleverson Rodrigues  
### Date: 12/22/2024  

### Support:
- Compatible with Python 3

---

### **Disclaimer:**
This is not an official Oracle application. It is not supported by Oracle Support and **SHOULD NOT** be used for usage calculation purposes.

---

## **Information:**
Script for tagging resources in a tenancy.

---

## **Connectivity:**
1. **User Authentication**  
   Use `$HOME/.oci/config`.  
   Refer to: [SDK Configuration Documentation](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/sdkconfig.htm)
2. **Instance Principals**  
3. **Delegation Token in Cloud Shell**  

---

## **Included Modules:**
- `oci.core.ComputeClient`
- `oci.core.BlockstorageClient`
- `oci.core.VirtualNetworkClient`
- `oci.identity.IdentityClient`
- `oci.load_balancer.LoadBalancerClient`
- `oci.database.DatabaseClient`
- `oci.object_storage.ObjectStorageClient`

---

## **Analysis of the Python Script for Tag Management in OCI**

This Python script is designed to manage resource tags in Oracle Cloud Infrastructure (OCI). Below is a detailed explanation of its main sections and functionalities.

### Imports

```python
import sys
import argparse
import datetime
import oci
import json
import os
```

- The script imports necessary libraries: `sys` for command-line argument manipulation, `argparse` for argument processing, `datetime` for timestamps, `oci` for interacting with OCI, `json` for formatting output, and `os` for system operations.

### Global Variables

```python
errors = 0
data = []
cmd = ""
```

- `errors`: Counter to track errors during execution.
- `data`: List to store information about the operations performed.
- `cmd`: Variable to store the processed command-line commands.

### Main Functions

#### 1. Displaying the Banner

```python
def print_banner(cmd, tenancy, assign_tags):
    ...
```

- Displays an informational banner about the script execution, including author information, start time, and command details.

#### 2. Argument Processing

```python
def command_line():
    ...
```

- Sets up the argument parser using `argparse`, allowing the user to specify options like configuration profile, proxy, compartments, regions, and actions to be performed (add, delete, or list tags).

#### 3. Creating a Signer

```python
def create_signer(config_profile, is_instance_principals, is_delegation_token):
    ...
```

- Creates an authentication object (`signer`) based on the provided credentials. Supports authentication via Instance Principals, Delegation Token, or configuration file.

#### 4. Checking Tag Namespaces

```python
def read_tag_namespaces(identity, tenancy, local_assign_tags):
    ...
```

- Checks if the specified tag namespaces exist in OCI, aborting execution if any namespace is not found.

#### 5. Loading Compartments

```python
def identity_read_compartments(identity, tenancy):
    ...
```

- Loads available compartments in OCI and filters as necessary.

#### 6. Handling Objects

```python
def handle_object(compartment, region_name, assign_tags, obj_name, list_object, update_object, update_modal_obj, availability_domains=None, namespace="", filter_by_name=""):
    ...
```

- Performs tag management operations on various resource types, such as instances, volumes, and load balancers. Updates tags as per the specified action.

#### 7. Handling Tags

```python
def handle_tags(defined_tags, freeform_tags, local_assign_tags):
    ...
```

- Adds, updates, or removes tags, both defined and free-form, according to user instructions.

### Command Usage

To filter and tag only resources containing the name "mkd", you can use the following command:

```bash
python3 auto_tag_navita.py -t DEFAULT -action add_defined -tag "pythonzero.autotag=hml" -filter_by_name "mkd"
```
To tag only the 'compute' resources that contain the name "mkd", you can modify the command as follows:
```bash
python3 auto_tag.py -t DEFAULT -action add_defined -tag "pythonzero.autotag=hml" -filter_by_name "mkd" -service type compute
```

- **Parameters**:
  - `-t DEFAULT`: Specifies the configuration profile to use.
  - `-action add_defined`: Action to add defined tags.
  - `-tag "pythonzero.autotag=hml"`: Defines the tag to be applied to the filtered resources.
  - `-filter_by_name "mkd"`: Filters resources that contain "mkd" in their names.

### Main Function

```python
def main():
    ...
```

- The main function orchestrates the script execution. It connects to OCI, processes command-line arguments, checks tag namespaces, and iterates over regions and compartments to apply tagging operations.

### Output

- The script displays output in JSON format or as a list, depending on the user-specified option. It also reports any errors that occurred during execution.

### Script Execution

```python
main()
```

- Calls the `main()` function to initiate the script execution.

---

## **Command-Line Parameters:**

| Parameter         | Description                                                                                      |
|-------------------|----------------------------------------------------------------------------------------------|
| `-t config`       | Configuration file to be used (tenancy profile).                                              |
| `-p proxy`        | Configure proxy (e.g., `www-proxy-server.com:80`).                                           |
| `-ip`             | Use Instance Principals for authentication.                                                  |
| `-dt`             | Use Instance Principals with delegation token in Cloud Shell.                                 |
| `-cp compartment` | Filter by compartment name or ID.                                                             |
| `-rg region`      | Filter by region name.                                                                         |
| `-action`         | Action to be performed: `add_defined`, `add_free`, `del_defined`, `del_free`, or `list`.      |
| `-tag`            | Tag information in the format `namespace.key=value` or `key=value`. Separate multiple tags with commas. |
| `-tagsep`         | Tag separator (default: comma).                                                                |
| `-service type`   | Service type (default: `all`). Available services: `all`, `compute`, `block`, `network`, `identity`, `loadbalancer`, `database`, `object`, `file`. |
| `-output`         | Output format: `list`, `json`, or `summary`.                                                  |
| `-filter_by_name` | Filter services by name. Separate multiple names with commas.                                  |

---