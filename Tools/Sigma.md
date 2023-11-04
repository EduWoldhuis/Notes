Sigma is like [[Yara]], but with some more options.



### Example rule
```
title: Example rule               // Required
status: Stable                    // Optional   // Options: Stable, Test, Experimental, Deprecated, Unsupported
description: This is an example.
author: Edu
reference
{arbitrary custom fields}

logsource:                        // Required   // Describes the log data
	product: Windows              // Optional   // Options: things like Windows, Apache and such
	category: wmi_event           // Optional   // Options: any category                            // wmi_event is a System monitoring event

detection:                        // Required
	test:                         // Required   // A name for your search function
		Commandline|contains:
			- '/var/log'          // Activated if the 
	{search-identifier}
```