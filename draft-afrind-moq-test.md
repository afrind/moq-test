---
title: "MoQT Test"
abbrev: "moq-test"
category: info

docname: draft-afrind-moq-test-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: ""
workgroup: "Media Over QUIC"
keyword:
 - moq
venue:
  group: "Media Over QUIC"
  type: ""
  mail: "moq@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/moq/"
  github: "afrind/moq-test"
  latest: "https://afrind.github.io/moq-test/draft-afrind-moq-test.html"

author:
 -
    fullname: "Alan Frindell"
    organization: Meta
    email: "afrind@meta.com"

normative:

informative:


--- abstract

This document specifies the moq-test protocol, a testing protocol for Media over QUIC (MOQ) implementations. moq-test utilizes the Track Namespace as parameters to the publisher, enabling flexible and customizable testing scenarios.

--- middle


# Introduction

moq-test is designed to provide a comprehensive testing framework for MOQ implementations. By leveraging the Track Namespace, moq-test allows testers to specify various parameters that control the testing process, such as forwarding preferences, group and object ranges, and object sizes.

# Track Namespace Parameters

The moq-test protocol utilizes the Track Namespace to convey testing parameters from the publisher to the subscriber. The Track Namespace tuple fields are defined as follows.  Exactly 16 tuple fields must be provided, but any blank tuple field other than field 0 indicates the default value is used.  Future versions will use a different value for tuple field 0 and potentially define a different set of parameters.

## Tuple Field 0: "moq-test-00"

This field indicates that the Track Namespace parameters are for the moq-test protocol.

## Tuple Field 1: Forwarding Preference

This field specifies the forwarding preference for the track. The following values are defined:

* 0 := One subgroup per group
* 1 := One subgroup per object
* 2 := Two subgroups per group (0 and 1)
* 3 := Datagram

The default value is 0.  When using two subgroups per group, even numbered objects are sent on subgroup 0 and odd numbered objects are sent on group 1.

## Tuple Field 2: Start Group

This field specifies the starting group number for the track.  The default value is 0.

## Tuple Field 3: Start Object

This field specifies the starting object number for the track.  The default value is 0.

## Tuple Field 4: Last Group in Track

This field specifies the last group number in the track.  The default value is 2^62 - 1 (eg: the track continues until unsubscribed).

Use caution when setting this field accessing this track via FETCH.

## Tuple Field 5: Last Object in Track

This field specifies the last object number in the track.  The maximum value is the number of objects in the group, plus one if end of group markers are used.

The default value is the maximum value.

## Tuple Field 6: Objects per Group

This field specifies the number of objects per group.  The default is 10.

## Tuple Field 7: Size of Object 0

This field specifies the size of the first object in bytes.  The default is 1024 bytes.

## Tuple Field 8: Size of Objects > 0

This specifies the size of objects with ID greater than 0, in bytes.  The default value is 100 bytes.

## Tuple Field 9: Object Frequency

This field specifies the frequency at which objects are sent in milliseconds.  The default value is 1000.

## Tuple Field 10: Group Increment

This field specifies the increment between group numbers.  The default is 1.

## Tuple Field 11: Object Increment

This tuple field specifies the increment between object numbers.  The default is 1.

## Tuple Field 12: Send End of Group Markers

This field specifies whether to send end of group markers.

* 0 := Do not send End of Group markers
* 1 := Send End of Group markers

End of group markers are an Object with status 0x3 End of Group.

The default value is 0 / No End of Group markers.

## Tuple Field 13: Test Integer Extension

This field specifies whether to include a test integer extension on every object in the track.  The ID of the extension is 2 * field value.  The value of the extension is random.

The default value indicates no integer extension is sent.

## Tuple Field 14: Test Variable Extensions

This field specifies whether to include a test variable extension on every object in the track.  The ID of the extension is 2 * field value + 1.  The value of the extension is data.

## Tuple Field 15: Publisher Delivery Timeout

This field specifies the publisher delivery timeout in milliseconds.   The default is no delivery timeout.

# Publisher Behavior

If any namespace tuple field other than zero is not a positive integer, or is outside the supported range of the publisher, a SUBSCRIBE_ERROR or FETCH_ERROR is returned.

Upon receiving a vald SUBSCRIBE or FETCH for a moq-test track, the publisher MUST publish objects according to the parameters of the namespace.

The payload of each published object is the charater t (for test) repeated for the length of the payload.

When Datagram forwarding preference is used, certain combinations of options result in objects that cannot be transmitted below the maximum datagram size.  The publisher either responds with SUBSCRIBE_ERROR if detected when handling the SUBSCRIBE, or SUBSCRIBE_DONE with an error code if detected when publishing the object.

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

The subscriber can generate an asymmetric amount of work on the server.  Servers SHOULD set limits on certain values such as object frequency and object size to prevent resource exhaustion attacks.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

Members of the Media over QUIC working group
