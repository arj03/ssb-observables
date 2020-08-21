# Log statements

In a distributed system such as SSB where each user maintains their
own immutable log, it might make sense to be able to write statements
about the messages in the log. One example would be contact messages
that needs to be processed in the correct order to make sense. If the
contact messages have no links between them directly, one needs to
traverse the whole log which can be time-consuming. A method for
adding information about messages is what log statements are about.

The idea is that anyone can write a statement saying that something
holds for a particular log, including but not limited to the author of
said log. The statement should be written in such a way that a third
party can verify the statement if needed.

Where this would come in handy is for partial replication where you
might replicate just the contact messages of a feed. The problem then
becomes, how do you know that the peer that gave you those messages
did not leave out any messages.

Log statements could also be used to attach information to existing
messages, such as content warning or similar metadata. Or to build an
index of the latest version of some mutable objects.

## Schema

The following schema is based on making statements about SSB feeds,
but could be extended to any log system with similar properties.

```
feed: @author
statement: the type of statement, example: contact messages
data: [seq1, seq2, ...]
point-in-time: latest seq for feed
version: 1
```

I imagine that log statements live outside the log that they are
describing. This would have the benefit that they could potentially be
much larger than messages on the log, and could be garbage collected
more easily.

## Validation

Multiple ways of verifying log statements might make sense. Consider
the contact messages example, assuming user A posts a statement about
a feed, another user B could verify this message and post a statement
verification. Another approach would be to simply post another log
statement. The second approach would probably work better in a system
such as SSB where the friend graph means that not every user can see
the same number of feeds.

If statements disagree about a fact, multiple ways of interpreting
this might make sense depending on the application.

## Replication

As the log statements do not need to be a part of the log they are
describing, a method for replicating these statements are
needed. Something along the lines of vector clocks, where the key is a
combination of feed and statement type. Probably with a special method
for querying statements to find new.

## History

I originally proposed log statements to written on the same log, but I
think it would be more general if they could reside outside a log as
well. This would be particularly useful in a scenario where the log
statements of a particular type would have a lot of version, but only
the latest would be releveant. If they are written outside of the main
log, as new versions are added the old can be safely forgotten.

## Related work

This work is related to snapshots in event sourcing systems.
