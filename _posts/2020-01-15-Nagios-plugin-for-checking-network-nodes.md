---
title: Nagios plugin for checking network nodes
tags: nagios monitoring
show_author_profile: true
---

## The Task
I was tasked with setting up monitoring of some network nodes where two of the nodes are used for redundancy of a system site and the third is a critical node that needs to override the others even if they also would fail to respond.

I did not find any plugin for Nagios that could represent the logic that I needed for creating the monitoring so I created this small shell script to use for the different specific sites that needed this.

## The Script

{% highlight shell linenos %}
#!/bin/bash

#	Created:	2021-01-13
#	Author:		Tim Wetterek Andersson
#	Contact:	tim@wetterek.se
# 
#	Description:	This is script is used as a plugin for Nagios/OP5 Monitor to check redundance of a set of network nodes.
#			
#			The "sites" I am checking consists of three type of nodes:
#			* SuperImportantNode
#			* RedundanceNodeType1
#			* RedundanceNodeType2
#
#			Checking if the nodes are alive will result in different exit codes described below.
#			
#			The reason for echo:ing the status of $column1_name is because of translations/custom messages used in production.
#
#	Dependencies:	* fping
#	

if [[ -z "$1" ]] 
then
	echo "check_network_nodes"
	echo ""
	echo "Syntax:" 
	echo "./check_network_nodes 'SuperImportantNode-Name,IP?RedundanceNodeType1-Name,IP?RedundanceNodeType2-Name,IP'"
	echo ""
	echo "Dependencies:"
	echo "* fping"
	echo ""
	echo "The plugin returns the following error codes based on these conditions:"
	echo "* OK (0) = All nodes are alive"
	echo "* Warning (1) = RedundanceNodeType1 OR RedundanceNodeType2 is unreachable"
	echo "* Critical (2) ="
	echo "  * RedundanceType1 AND RedundanceType2 is unreachable"
	echo "  * SuperImportantNode is unreachable (Overrides status of RedundanceNodes)"
	echo "* Unknown (3) = Wrong formatting of argument or missing dependencies"
	echo ""
	exit 3
fi

# Get input arguments and replace "?" to newline.
var_input=$(echo $1 | sed 's/?/\n/g')
var_exitcode=0

# Start reading of the "csv" as columns 1 and 2.
while IFS="," read -r column1_name column2_IP; do
	var_status=$(fping $column2_IP)
  
	if [[ $var_status == *"alive"* ]]; then
		echo "OK!" $column1_name "is alive"

# Differentiate different statuses. If SuperImportantNode is unreachable, this should always result in a exit code of 2/Critical.
  	elif [[ $var_status == *"unreachable"* ]]; then
		if [[ $column1_name == *"SuperImportantNode"* ]]; then
			var_exitcode=2
			echo "Critical!" $column1_name "is unreachable"
		
		elif [[ $column1_name == *"RedundanceNodeType1"* ]] || [[ $column1_name == *"RedundanceNodeType2"* ]] && [[ $var_exitcode != 2 ]]; then
			var_exitcode=$(($var_exitcode + 1))
			echo "Warning!" $column1_name "is unreachable"
			
		elif [[ $column1_name == *"RedundanceNodeType1"* ]] || [[ $column1_name == *"RedundanceNodeType2"* ]] && [[ $var_exitcode == 2 ]]; then
			echo "Warning!" $column1_name "is unreachable"
			
		else
			var_exitcode=3
			echo "Error! Check input arguments/syntax"
			
		fi

	else
		var_exitcode=3
		echo "Error! Check input arguments/syntax"

	fi

# Get the input arguments and read to to "while"  
done < <(echo "$var_input")

exit $var_exitcode
unset 1
unset var_input
unset var_exitcode
unset var_status
unset column1_name
unset column2_IP
{% endhighlight %}
