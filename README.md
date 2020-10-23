# SSB observables

In a distributed system such as [SSB] where each user maintains their
own immutable log, it might make sense to be able to write statements
about the messages in the log. One example would be contact messages
that needs to be processed in the correct order to make sense, or a
person pinning specific messages as the most *interesting* (whatever
that means) messages for that feed.

If the contact messages have no links between them directly, one needs
to traverse the whole log which can be time-consuming. Furthermore
lets say one wants to do partial replication of only contact messages
from an untrusted third party, how do you make sure they didn't leave
out any messages in the middle.

If one could write a snapshot of what messages are contact messages
and distribute that in some form, that would solve both problems. As
new contact messages are added to the log, the state of the snapshot
needs to be updated, and the old information is no longer relevant so
can be safely disgarding. Thinking more broadly about this concept,
what it really models is values changing over time where you don't
need the history and this is exactly what observables are.

Broadening this concept so you are not only describing something on
your own feed, but instead on another feed opens up more use
cases. One example would be another person making statements about a
feeds *important* messages. Or in the case of contact messages,
another feed confirming the validity of the contact snapshot by
posting their own snapshot of the same information.

## Discovery

One problem then becomes, how do you know which observables for a
particular feed exists. Since we already are using this in a system
that support partial replication, we can use a specific message type
for these kind of messages and use that as the discovery
mechanism. The actual content of the observable would reside outside
of the chain, in the same way blobs are also not part of the
chain. Furthermore, these messages could be encrypted to only a subset
of other users using private messages or groups.

## Schema

The following schema for the content of the observable is based on
making statements about SSB feeds or a particular messages of a feed,
but could be extended to any log system with similar properties.

```
feed: @author
type: e.g. contact-messages
data: [seq1, seq2, ...]
point-in-time: latest seq for feed
version: 1
```

## Replication

Observables are very close to a versioned blob. So it is natural to
think of both the existing blob protocol and also EBT as a way to
distribute the data.

Basing it on the blob protocol has several downsides. It requires a
new messages on the log for each new version of the observable. It
would require garbage collection as the old versions the blobs are not
really useful and lastly it would require something to actively
request the blobs.

Instead EBT will be used as that solves all of the downsides directly
at the protocol level by only having one atomically updated file for
each observable and pushing new versions as the obserable is
updated. Since the data is signed and the version number always
increases, data can be safely distributed between untrusted peers.

As an optimization, by storing a window of the latest versions, it
would be possible to send diffs instead of the whole state when
replicating.

## Data format

FIXME: stricker definition of this, but loosely

- Encoding + hashing algorithm used
- Encoded content as defined in schema
- Hash of encoded data
- Signature of everything above

## History

I originally proposed log statements (that was the original name) to
written on the same log, but I think it would be more general if they
could reside outside a log as well. This would be particularly useful
in a scenario where the log statements of a particular type would have
a lot of versions, but only the latest would be releveant. In this
case old versions can be safely forgotten.

Thanks to regular a much more succinct name for this project.

## Related work

This work is related to snapshots in event sourcing systems.

While it would have been better to design something like contact
messages like [ssb-profile] in the first place, one cannot change the
past and furthermore the system is more general than patching up bad
application message formats.

Discussed on SSB: %Z3xS6WmnIVjwwynBOkdz3azct/2Es/DvgrMfNEAPUis=.sha256

[SSB]: https://github.com/ssbc
[ssb-profile]: https://gitlab.com/ahau/ssb-plugins/ssb-profile
