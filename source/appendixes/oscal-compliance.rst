.. Coyright (C) 2020 GovReady PBC

.. _OSCAL Compliance:

OSCAL Compliance Notes
======================

GovReady-Q supports OSCAL version 1.0.0 RC1 in the following
scenarios:

* Import of OSCAL components in JSON format
* Export of OSCAL components in JSON format
* Export of OSCAL SSP in JSON format

The OSCAL specification is complex and subject to change.  Refer to
this appendix for information on how GovReady-Q currently implements
the OSCAL specificaiton.

OSCAL Component Implementation Notes
------------------------------------
GovReady-Q will only import OSCAL component files that are valid
according to the OSCAL version 1.0.0 RC1 schema.

When importing an OSCAL component, GovReady-Q expects to find the
control statement narratives in OSCAL *statement* elements with a
*statement_id* that refers to an element in the corresponding OSCAL
catalog.

When exporting an OSCAL component, GovReady-Q emits the control
statement narratives using OSCAL *statement* elements.

When importing and exporting OSCAL components, the *source* element
contained within an *control_implementation* should be a catalog
identifier, not a URI as per the OSCAL specification.  This
deficiency will be corrected in the future.  Valid source identifiers
include:

* NIST_SP-800-53_rev4
* NIST_SP-800-53_rev5
* NIST_SP-800-171_rev1

OSCAL SSP Implementation Notes
------------------------------
To export OSCAL SSP in JSON format, you must use a Compliance App that
includes the OSCAL SSP JSON output template. An OSCAL SSP JSON output template
can be found in the included the General IT System ATO (v1.0.1) and the 
Lightweight_ATO_Template > light-ato-ssp (v0.2.9).

While the OSCAL SSP JSON that GovReady-Q produces is valid according
to the OSCAL SSP JSON schema, many optional elements are currently
omitted.  The component and control implementations, including
organizational parameters, are relatively complete, however.

The completeness and fidelity of the OSCAL SSP JSON representation
will continue to improve over time.



