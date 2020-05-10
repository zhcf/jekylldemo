---
layout: page
title: Noderange Syntax
permalink: /documentation/noderange.html
---

Confluent implements a powerful language to indicate a target
set of nodes.  It incorporates the concepts of ranged names,
regular expression search, attribute criteria match, groups,
and set arithmetic.

The simplest noderange is a single node name:

	n1

Nodes and groups may be used interchangeably.  The following may
be used in any context where n1 would be accepted as a noderange:

	rack1

Commonly, there is a desire to target a range of elements.  There are
a few identically behaving syntaxes for the purpose.

	n1:n20
	n1-n20
	n[1-20]
	

Note that numbers may be zero padded or not, it will automatically detect
the padding amount and adjust members of the range accordingly.  Ranges
also can understand multiple numeric values changing:

	r[1-3]u[01-10]
	r1u01-r3u10

Ranges can also be applied to group names, and all above syntaxes are compatible:

	rack1-rack10
	rack[1-10]

Also, regular expressions may be used to indicate nodes with names matching certain patterns:

	~r1u..

The other major noderange primitive is indicating nodes by some attribute value:

	location.rack=7

Commas can be used to indicate multiple nodes, and can mix and match any of the above primitives.  The following can be
a valid single noderange, combining any and all members of each comma separated component

	n1,n2,rack1,storage,location.rack=9,~s1..,n20-n30

Exclusions can be done by prepending a '-' before a portion of a noderange:

	rack1,-n2
	compute,-rack1
	compute,-location.row=12

To indicate nodes that match multiple selections at once (set intersection), the @ symbol may be used:

	compute@rack1
	location.rack=10@compute

For complex expressions, () may be used to indicate order of expanding the noderange to be explicit

	rack1,-(console.logging=full@compute)

Noderange syntax can also indicate 'pagination', or separating the nodes into well defined chunks.  > is used to indicate
how many nodes to display at a time, and < is used to indicate how many nodes to skip into a noderange:

	rack1>3<6

The above would show the seventh through ninth nodes of the rack1 group.  Like all other noderange operations, this may be combined
with any of the above, but must appear as the very last operation.  Ordering is done with a natural sort.

