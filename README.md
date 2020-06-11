# Log statements

In a distributed system such as SSB where each user maintains their
own immutable log, it might make sense to be able to write statements
about the messages in the log. One example would be contact messages
that needs to be processed in the correct order to make sense. If the
contact messages have no links between them directly, one needs to
traverse the whole log which can be time-consuming. A method for
adding information about messages to an existing log is what log
statements is about.

The idea is that anyone can write a statement saying that something
holds for a particular log, including but not limited to the author of
said log. The statement should be written in such a way that a third
party can verify the statement if needed.

Where this would come in handy is for partial replication where you
might replicate just the contact messages of a feed. The problem then
becomes, how do you know that the peer that gave you those messages
did not leave out any messages.

Log statements could also be used to attach information to existing
messages, such as content warning or similar metadata.

## Schema

The following schema is based on SSB, but could be extended to any log
system with similar properties.

```
feed: @author
statement: the type of statement, example: contact messages
date: [seq1, seq2, ...]
point-in-time: latest seq for feed
previous: %id of previous statement about the same feed and type
```

TODO: would it make sense to tangle these?
TODO: define if a new message linking to previous overwrites or extends data

## Validation

Multiple ways of verifying log statements might make sense. Consider
the contact messages example, assuming user A posts a message about a
feed, another user B could verify this message and post a statement
verification message. Another approach would be to simply post another
log statement. This second approach would probably work better in a
system such as SSB where the friend graph means that not every user
can see the same number of feeds.

If statements disagree about a fact, multiple ways of interpreting
this might make sense depending on the application.
