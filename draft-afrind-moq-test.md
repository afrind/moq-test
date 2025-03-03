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

The moq-test protocol utilizes the Track Namespace to convey testing parameters from the publisher to the subscriber. The Track Namespace tuples are defined as follows.  Exactly 16 tuples must be provided, but any blank tuple field other than tuple 0 indicates the default value is used.  Future versions will use a different Tuple 0 and potentially define a different set of parameters.

## Tuple 0: "moq-test-00"

This tuple indicates that the Track Namespace parameters are for the moq-test protocol.

## Tuple 1: Forwarding Preference

This tuple specifies the forwarding preference for the track. The following values are defined:

* 0 := One subgroup per group
* 1 := One subgroup per object
* 2 := Two subgroups per group (0 and 1)
* 3 := Datagram

The default value is 0.  When using two subgroups per group, even numbered objects are sent on subgroup 0 and odd numbered objects are sent on group 1.

## Tuple 2: Start Group

This tuple specifies the starting group number for the track.  The default value is 0.

## Tuple 3: Start Object

This tuple specifies the starting object number for the track.  The default value is 0.

## Tuple 4: Last Group in Track

This tuple specifies the last group number in the track.  The default value is 2^62 - 1 (eg: the track continues until unsubscribed).

Use caution when setting this field accessing this track via FETCH.

## Tuple 5: Last Object in Track

This tuple specifies the last object number in the track.  The default value is the number of objects in the group, plus one if end of group markers are used.

## Tuple 6: Objects per Group

This tuple specifies the number of objects per group.  The default is 10.

## Tuple 7: Size of Object 0

This tuple specifies the size of the first object in bytes.  The default is 1024 bytes.

## Tuple 8: Size of Objects > 0

This tuple specifies the size of objects with ID greater than 0, in bytes.  The default value is 100 bytes.

## Tuple 9: Object Frequency

This tuple specifies the frequency at which objects are sent in milliseconds.  The default value is 1000.

## Tuple 10: Group Increment

This tuple specifies the increment between group numbers.  The default is 1.

## Tuple 11: Object Increment

This tuple specifies the increment between object numbers.  The default is 1.

## Tuple 12: Send End of Group Markers

This tuple specifies whether to send end of group markers.

* 0 := Do not send End of Group markers
* 1 := Send End of Group markers

End of group markers are an Object with status 0x3 End of Group.

The default value is 0 / No End of Group markers.

## Tuple 13: Test Integer Extension

This tuple specifies whether to include a test integer extension on every object in the track.  The ID of the extension is 2 * field value.  The value of the extension is random.

The default value indicates no integer extension is sent.

## Tuple 14: Test Variable Extensions

This tuple specifies whether to include a test variable extension on every object in the track.  The ID of the extension is 2 * field value + 1.  The value of the extension is data.

## Tuple 15: Publisher Delivery Timeout

This tuple specifies the publisher delivery timeout in milliseconds.   The default is no delivery timeout.

# Publisher Behavior

If any namespace tuple field other than zero is not a positive integer, or is outside the supported range of the publisher, a SUBSCRIBE_ERROR or FETCH_ERROR is returned.

Upon receiving a vald SUBSCRIBE or FETCH for a moq-test track, the publisher MUST publish objects according to the parameters of the namespace.

The payload of each published object is the charater t (for test) repeated for the length of the payload.

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
