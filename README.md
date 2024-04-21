# Subscription Level Logging

<h2>Purpose</h2>

- Configure subscription level logging and forwarding to Log Analytics Workspace.
- Analyze some subscription level logs on LAW.

VMs do not need to be on for this lab.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/Subscription%20Level%20Logging%20diagram.png"/>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/Subscription%20Level%20Logging%20diagram%203.png"/>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/Subscription%20Level%20Logging%20diagram%202.png"/>

#
<h3>Configuring Subscription Level Logging</h3>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/1.png"/>

Go to the Monitor blade, then to Activity log. Select “Export Activity Logs”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/2.png"/>

Choose the Subscription you want to monitor/log and select “+ Add diagnostic setting”.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/3.png"/>

Give the diagnostic setting a name. Select every option under Categories and the LAW you want to send the logs to. Select Save.

#
<h3>Generating and Analyzing Logs</h3>

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/4.png"/>

Create two Resource groups:
- Critical-Infrastructure-Wastewater
- Scratch-Resource-Group

Delete the Resource groups after. This will generate logs relating to resource groups for subscription level logging.

#
// Querying for the deletion of critical Resource Groups:

AzureActivity

| where ResourceGroup startswith "Critical-Infrastructure-"

| order by TimeGenerated

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/5.png"/>

This query puts out logs for the ResourceGroup Critical-Infrastructure-Wastewater, and formats the results in order of TimeGenerated. This query essentially shows the existence of these resource groups in the subscription.

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/6.png"/>

As show in previous labs, create an inbound security rule for the attack-vm network security group to allow all inbound traffic. This will generate network security group related logs for subscription level logging.

#
// Querying for changes to network security groups:

AzureActivity

| where OperationNameValue == "MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/SECURITYRULES/WRITE"

// Optionally, specific Resource Groups:

| where ResourceGroup in ("resource-group-1", "resource-group-2")

| order by TimeGenerated

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/7.png"/>

This query displays the creation and activation of the NSG inbound rule created prior.

#
// Deletion activities within a certain timespan:

AzureActivity

| where OperationNameValue endswith "DELETE"

| where ActivityStatusValue == "Success"

| where TimeGenerated > ago(30m)

| order by TimeGenerated

<img src="https://raw.githubusercontent.com/melisaaaaaaaaa-er/subscription-level-logging-images/main/8.png"/>

This query displays the successful deletion of resource groups from within the past 30 minutes, and ordered by TimeGenerated.
