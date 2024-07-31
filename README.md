# linux-mde-failed-extensions
This KQL (Kusto Query Language) script is designed to identify and summarize failed extensions on hybrid compute machines. Here’s a breakdown of what it does:

# Overview

# The script performs the following steps:

Filter Machines: Filters resources to include only those of type microsoft.hybridcompute/machines.
Project Machine Details: Selects specific properties of these machines, including:
id: The unique identifier of the machine.
JoinID: The machine ID converted to uppercase for joining purposes.
ComputerName: The computer name from the machine’s OS profile.
OSName: The operating system name.

Join with Extensions: Performs a left outer join with resources of type microsoft.hybridcompute/machines/extensions, matching machines with their corresponding extensions based on the machine ID.

Project Extension Details: Selects various properties of the extensions, such as:

MachineId: The machine ID extracted from the extension ID.
ExtensionName
ExtensionStatus
ExtensionMessage
ExtensionType
Other extension-specific properties.

Filter Failed Extensions: Filters the results to include only extensions with a status of Failed.

Summarize Extensions: Summarizes the failed extensions for each machine, creating a list of extension details for each machine. The summary includes properties like the extension name, status, message, type, and various settings.

Order by OS Name: Orders the results by the operating system name in ascending order.

## Usage
To use this script, simply run it in your Kusto environment. Ensure you have the necessary permissions to access the microsoft.hybridcompute/machines and microsoft.hybridcompute/machines/extensions resources.

# Example
// Filter Machines
let machines = 
    resources
    | where type == "microsoft.hybridcompute/machines"
    | project id, JoinID = toUpper(id), ComputerName = properties.osProfile.computerName, OSName = properties.osProfile.osName;

// Join with Extensions
let extensions = 
    resources
    | where type == "microsoft.hybridcompute/machines/extensions"
    | project MachineId = extract(".*machines/(.*)/extensions/.*", 1, id), ExtensionName = name, ExtensionStatus = properties.provisioningState, ExtensionMessage = properties.status.message, ExtensionType = properties.type;

let failedExtensions = 
    machines
    | join kind=leftouter (extensions) on $left.JoinID == $right.MachineId
    | where ExtensionStatus == "Failed"
    | summarize Extensions = make_list(pack("ExtensionName", ExtensionName, "ExtensionStatus", ExtensionStatus, "ExtensionMessage", ExtensionMessage, "ExtensionType", ExtensionType)) by id, ComputerName, OSName
    | order by OSName asc;

failedExtensions

Feel free to submit issues or pull requests if you have suggestions for improvements or find any bugs.
