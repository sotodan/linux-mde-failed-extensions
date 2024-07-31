# linux-mde-failed-extensions
This KQL (Kusto Query Language) script is designed to identify and summarize failed extensions on hybrid compute machines. Here’s a breakdown of what it does:

Filter Machines: It starts by filtering resources to include only those of type microsoft.hybridcompute/machines.
Project Machine Details: It then projects (selects) specific properties of these machines, including:
id: The unique identifier of the machine.
JoinID: The machine ID converted to uppercase for joining purposes.
ComputerName: The computer name from the machine’s OS profile.
OSName: The operating system name.
Join with Extensions: The script performs a left outer join with another set of resources of type microsoft.hybridcompute/machines/extensions. This join matches machines with their corresponding extensions based on the machine ID.
Project Extension Details: It projects various properties of the extensions, such as:
MachineId: The machine ID extracted from the extension ID.
ExtensionName, ExtensionStatus, ExtensionMessage, ExtensionType, and other extension-specific properties.
Filter Failed Extensions: It filters the results to include only extensions with a status of ‘Failed’.
Summarize Extensions: It summarizes the failed extensions for each machine, creating a list of extension details for each machine. The summary includes properties like the extension name, status, message, type, and various settings.
Order by OS Name: Finally, it orders the results by the operating system name in ascending order.
