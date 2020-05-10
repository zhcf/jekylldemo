---
layout: page
title: Attribute Expressions
permalink: /documentation/attributeexpressions.html
---

In confluent, any attribute may either be a straightforward value, or an expression
to generate the value.

An expression will contain some directives wrapped in '{}' characters.  Within {} are
a number of potential substitute values and operations.

The most common operation is to extract a number from the nodename.  These values are available
as n1, n2, etc.  So for example attributes for a node named b1o2r3u4 would have {n1} as 1,
{n2} as 2, {n3} as 3, and {n4} as 4.  Additionally, {n0} is special as representing the *last*
number in a name, so in the b1o2r3u4 example, {n0} would be 4.

Frequently a value derives from a number in the node name, but must undergo a transform to be
useful.  As an example, if we have a scheme where nodes are numbered n1-n512, and they are arranged
1-42 in rack1, 43-84 in rack2, and so forth, it is convenient to perform arithmetic on the extracted
number.  Here is an example of codifying the above scheme, and setting the u to the remainder:

	location.rack=rack{(n1-1)/42+1}
	location.u={(n1-1)%42+1}

Note how text may be mixed into expressions, only data within {} will receive special treatment.
Here we also had to adjust by subtracting 1 and adding it back to make the math work as expected.

It is sometimes the case that the number must be formatted a different way, either specifying 0 padding
or converting to hexadecimal.  This can be done by a number of operators at the end to indicate formatting changes.

	{n1:02x} - Zero pad to two decimal places, and convert to hexadecimal, as might be used for generating MAC addresses
	{n1:x} - Hexadecimal without padding, as may be used in a generated IPv6 address
	{n1:X} - Uppercase hexadecimal
	{n1:02d} - Zero pad a normal numeric representation of the number.

Another common element to pull into an expression is the node name in whole:

	hardwaremanagement.manager={nodename}-imm

Additionally other attributes may be pulled in:

	hardwaremanagement.switchport={location.u}

Multiple expressions are permissible within a single attribute:

	hardwaremanagement.manager={nodename}-{hardwaremanagement.method}

A note to developers: in general the API layer will automatically recognize a generic
set attribute to string with expression syntax and import it as an expression.  For example,
submitting the following JSON:

	{ 'location.rack': '{n1}' }

Will auto-detect {n1} as an expression and assign it normally.  If wanting to set that value
verbatim, it can either be escaped by doubling the {} or by explicitly declaring it as a value:

	{ 'location.rack': '{% raw %}{{n1}}{% endraw %}' }

	{ 'location.rack': { 'value': '{n1}' } }



