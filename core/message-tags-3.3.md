---
title: IRCv3.3 Message Tags
layout: spec
work-in-progress: true
copyrights:
  -
    name: "Kiyoshi Aman"
    period: 2016
    email: "kiyoshi.aman@gmail.com"
  -
    name: "James Wheare"
    period: 2016
    email: "james@irccloud.com"
---
## Introduction

This specification adds a new capability for general message tags support and
a prefix for expressing client-only tags.

## Motivation

Previously, clients were required to negotiate a capability with servers for each
supported tag. This made tags inappropriate for client-only features. By adding a
new base capability, this specification allows clients to indicate support for
receiving any well-formed tag, whether or not it is recognised or used. This also
frees servers from having to filter messages tags for each individual client
response.

The client-only tag prefix allows servers to safely relay untrusted client tags,
keeping them distinct from server-initiated tags that carry verified meaning.

## Architecture

### Capabilities

This specification adds the `message-tags` capability. Clients requesting
this capability indicate that they are capable of parsing all well-formed tags,
even if they don't handle them specifically.

Servers advertising this capability indicate that they are capable of handling
client-only tags.

### Tags

Client-only tags are client-initiated tags that servers MUST attach as-is
to any relevant event relayed to other clients. A client-only tag is prefixed
with a plus sign (`+`) and otherwise conforms to the format specified in
[IRCv3.2 tags](./message-tags-3.2.html).

Client-only tags MUST be relayed on `PRIVMSG` and `NOTICE` events, and
MAY be relayed on other events.

The expected client behaviour of individual client-only tags SHOULD be defined
in separate specifications, in the same way as server-initiated tags.

This means client-only tags that aren't specified in the IRCv3 extension registry MUST
use a vendor prefix and SHOULD be submitted to the IRCv3 working group for consideration
if they are appropriate for more widespread adoption.

The updated BNF for keys is as follows:

    <key>           ::= [ '+' ] [ <vendor> '/' ] <sequence of letters, digits, hyphens (`-`)>

Individual tag keys MUST only be used a maximum of once per message. Clients
receiving messages with more than one occurrence of a tag key SHOULD discard all
but the final occurrence.

## Security considerations

Client-only tags should be treated as untrusted data. They can contain any value
and are not validated by servers in any way.

## Client implementation considerations

There is no guarantee that other clients will support or display the client-only
tags they receive, so these tags should only be used to enhance messages with non-critical
information. Messages should still make sense to clients with no support for tags.

## Moderation considerations

This section is non-normative.

Moderation tools available to channel and network operators typically operate on message
text and sender information only. To avoid abusive content being sent in client-only tags,
servers, services and management bot implementations may wish to enable moderation features
that act on tag content as well.

## Examples

This section is non-normative.

A bot that provides titles for URLs shared in channel could use a client-only
tag to provide an icon enhancement for its response:

```
:nick!user@example.com PRIVMSG #channel :https://example.com/a-news-story
@+icon=https://example.com/favicon.png :url_bot!bot@example.com PRIVMSG #channel :Example.com: A News Story
```

An example of a vendor-prefixed client-only tag:

```
@+example.com/foo=bar :irc.example.com NOTICE #channel :A vendor-prefixed client-only tagged message
```
