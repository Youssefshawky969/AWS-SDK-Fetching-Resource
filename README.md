# Fetch VPC and EC2 Details

[section:overview]

### Overview
In the section we are going to fetch AWS resources details in the region `us-east-1` (eg. VPCs and EC2 instances), then save all deatils in JSON or text format.

[section:implementing steps]
### Implemneting Steps

 **Make sure you have the AWS SDK for Python (Boto3) installed.**
```
pip install boto3
```

**Configure AWS Credentials**
```
aws configure
```

**Or, you can set them in your script:**
```
import boto3

session = boto3.Session(
    aws_access_key_id="YOUR_ACCESS_KEY",
    aws_secret_access_key="YOUR_SECRET_KEY",
    region_name="us-east-1"
)
```

**Improting required libreires**
```
import boto3
import json
```
**then Initialize the session and clients calls**
```
session = boto3.Session(region_name="us-east-1")
ec2_client = session.client("ec2")
```

**Fetch VPC details**
```
vpc_response = ec2_client.describe_vpcs()
vpcs = vpc_response.get("Vpcs", [])
```
`describe_vpcs()` here Calls the EC2 API to get a list of all VPCs in the us-east-1 region.
`.get("Vpcs", [])` here Safely extracts the list of VPCs from the response. If the key doesn't exist, it returns an empty list.

**Fetch EC2 details**
```
ec2_response = ec2_client.describe_instances()
instances = []
```

`describe_instances()` here Fetches details about all EC2 instances in the region.
`instances = []` here Initializes an empty list to collect instance data.

```
for reservation in ec2_response["Reservations"]:
    for instance in reservation["Instances"]:
        instances.append(instance)
```
As we said before AWS wraps EC2 instance info in a `Reservations` list. You loop over each reservation, and then loop again to get each instance. then `instances.append(instance)` here Adds each EC2 instance's details to your instances list.

**Create a dictionary to store results**

```
data = {
    "VPCs": vpcs,
    "EC2_Instances": instances
}
```
You organize your data into a dictionary with two keys: `VPCs` and `EC2_Instances`.

**Save data as JSON**

```
with open("aws_details.json", "w") as json_file:
    json.dump(data, json_file, indent=4)
```

`open("aws_details.json", "w")` Opens a file for writing.
`json.dump(data, json_file, indent=4)` Writes the Python dictionary into the file in pretty-printed JSON format.

Finally, confirms to the user that the task is complete and the data has been saved.
```
print("AWS details have been saved to aws_details.json")
```

**Full Code**
```
import boto3
import json

# Initialize the session and clients
session = boto3.Session(region_name="us-east-1")
ec2_client = session.client("ec2")

# Fetch VPC details
vpc_response = ec2_client.describe_vpcs()
vpcs = vpc_response.get("Vpcs", [])

# Fetch EC2 details
ec2_response = ec2_client.describe_instances()
instances = []

for reservation in ec2_response["Reservations"]:
    for instance in reservation["Instances"]:
        instances.append(instance)

# Create a dictionary to store results
data = {
    "VPCs": vpcs,
    "EC2_Instances": instances
}

# Save data as JSON
with open("aws_details.json", "w") as json_file:
    json.dump(data, json_file, indent=4)

print("AWS details have been saved to aws_details.json")
```


