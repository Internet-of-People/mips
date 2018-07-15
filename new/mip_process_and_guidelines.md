---
mip: 1
title: MIP Purpose and Guidelines
status: Draft
type: Process
author: Byron Blattel (@alchebyte) and others
created: 2018-07-13
---

## What is a MIP?

MIP stands for Mercury Improvement Proposal. A MIP is a design document providing information to the Libertaria community, or describing a new feature for Mercury or its processes or environment. The MIP should provide a concise technical specification of the feature and a rationale for the feature. The MIP author is responsible for building consensus within the community and documenting dissenting opinions.

## MIP Rationale

We intend MIPs to be the primary mechanisms for proposing new features, for collecting community input on an issue, and for documenting the design decisions that go into Mercury. Because the MIPs are maintained as text files in a versioned repository, their revision history is the historical record of the feature proposal.

For Mercury implementers, MIPs are a convenient way to track the progress of their implementation. Ideally each implementation maintainer would list the MIPs that they have implemented. This will give end users a convenient way to know the current status of a given implementation or library.

## MIP Types

There are three types of MIP:

- A **Standards** MIP describes any change that affects most or all Mercury implementations, such as a change to the network protocol, a shared schema, proposed application standards/conventions, or any change or addition that affects the interoperability of applications using Mercury. Furthermore Standards MIPs can be broken down into the following categories. Standards MIPs consist of three parts, a design document, implementation, and finally if warranted an update to the [formal specification].
  - **Core** - includes improvements around...
  - **Networking** - includes improvements around...
  - **Protocol** - includes improvements around client-server and server-server protocols
  - **Schema** - API-level standard schemas for shared structured data (ex. profiles/relationships).
- An **Informational** MIP describes an Mercury design issue, or provides general guidelines or information to the Mercury community, but does not propose a new feature. Informational MIPs do not necessarily represent Mercury community consensus or a recommendation, so users and implementers are free to ignore Informational MIPs or follow their advice.
- A **Process** MIP describes a process surrounding Mercury or proposes a change to (or an event in) a process. Process MIPs are like Standards MIPs but apply to areas other than the Mercury protocol itself. They may propose an implementation, but not to Mercury's codebase; they often require community consensus; unlike Informational MIPs, they are more than recommendations, and users are typically not free to ignore them. Examples include procedures, guidelines, changes to the decision-making process, and changes to the tools or environment used in Mercury development.

It is highly recommended that a single MIP contain a single key proposal or new idea. The more focused the MIP, the more successful it is likely to be. A change to one client doesn't require an MIP; a change that affects multiple clients, or defines a standard for multiple apps to use, does.

An MIP must meet certain minimum criteria. It must be a clear and complete description of the proposed enhancement. The enhancement must represent a net improvement. The proposed implementation, if applicable, must be solid and must not complicate the protocol unduly.

## MIP Work Flow

Parties involved in the process are you, the champion or *MIP author*, the [*MIP editors*](#mip-editors), and the [*Libertaria Developers*](https://github.com/orgs/libertaria-project/teams/devs).

:warning: Before you begin, vet your idea, this will save you time. Ask the Mercury community first if an idea is original to avoid wasting time on something that will be be rejected based on prior research (searching the Internet does not always do the trick). It also helps to make sure the idea is applicable to the entire community and not just the author. Just because an idea sounds good to the author does not mean it will work for most people in most areas where Mercury is used. Examples of appropriate public forums to gauge interest around your MIP include [the Mercury Discord channel], [the Issues section of this repository], and [one of the Libertaria Discord or Telegram channels]. In particular, [the Issues section of this repository] is an excellent place to discuss your proposal with the community and start creating a more formalized language around your MIP.

Your role as the champion is to write the MIP using the style and format described below, shepherd the discussions in the appropriate forums, and build community consensus around the idea. Following is the process that a successful MIP will move along:

```
[ DRAFT ] -> ( VOTE ) -> [ ACCEPTED ] -> [ FINAL ] ... [ *SUPERCEDED* ]
                                      -> [ DEFERRED ]
                         [ REJECTED ]
```

Each status change is requested by the MIP author and reviewed by the MIP editors. Use a pull request to update the status. Please include a link to where people should continue discussing your MIP. The MIP editors will process these requests as per the conditions below.

* **Draft** -- Once the champion has asked the Mercury community whether an idea has any chance of support, they will write and submit a draft MIP as a [pull request]. The PR will initiate a discussion with the MIP editor(s) to prepare the **Draft** MIP for a vote.
  * :arrow_right: **Vote** (*pending*) -- If/when requirements for MIP submission are met, a MIP editor will assign the MIP a number (generally the PR number related to the MIP) and merge your pull request. The MIP editor will not unreasonably deny an MIP.
  * :x: **Draft** -- Reasons for denying **Vote** (*pending*) status include being too unfocused, too broad, duplication of effort, being technically unsound, not providing proper motivation or addressing backwards compatibility, or not in keeping with the [Libertaria Bluepaper](https://drive.google.com/file/d/10dRjiuRzcC3TlaKGYFA4duG7FlfUtH7L/view).
* **Vote** -- Once the first draft has been merged, you may submit follow-up pull requests with further changes to your MIP until such point as you believe the MIP to be mature and ready to proceed to the next status via a Core/Community vote. To advance the MIP to a vote, add the 'VOTE' tag to the draft MIP PR. This will notify the MIP editors to put the MIP up for vote by the Core/Community. The vote will have one of four outcomes-
  * :arrow_right: **Accepted** (Standards MIPs) -- The Libertaria Developers will assign a target SDK version for release of the implementation. Once implemented the **Accepted** MIP will 'automatically' become **Final** for the SDK version it was first implemented in.
  * :arrow_right: **Final** (Information and Process MIPs) -- A successful **Vote** without material changes or unaddressed complaints will become Final.
  * :x: **Rejected** -- Voting results in community closing this MIP submission permanently. The draft PR will remain in the MIPs repo for historical purposes.
  * :x: **Draft** --  Voting results in material changes or substantial unaddressed complaints will cause the MIP to revert to **Draft**.
* **Accepted (Core MIPs only)** -- This is being implemented by Libertaria Developers.
  * :arrow_right: **Final** -- Standards MIPs must be implemented before they can be considered **Final**. When the implementation is complete and supported by the community, the status will 'automatically' be considered **Final**.
* **Final** -- This MIP represents the current state-of-the-art. A Final MIP should only be updated to correct errata.

Other exceptional statuses include:

* **Deferred** -- This is for **Accepted** (Standards) MIPs that have been delayed to a later SDK for implemenation (for technical or other reasons).
* **Superseded** -- An MIP which was previously **Final** but is no longer considered state-of-the-art. Another MIP will be in Final status and reference the Superseded MIP.

## What belongs in a successful MIP?

Each MIP should have the following parts:

- Preamble - RFC 822 style headers containing metadata about the MIP, including the MIP number, a short descriptive title (limited to a maximum of 44 characters), and the author details. See [below](https://github.com/libertaria-project/MIPs/blob/master/MIPS/mip-1.md#mip-header-preamble) for details.
- Simple Summary - “If you can’t explain it simply, you don’t understand it well enough.” Provide a simplified and layman-accessible explanation of the MIP.
- Abstract - a short (~200 word) description of the technical issue being addressed.
- Motivation (*optional) - The motivation is critical for MIPs that want to change the Mercury protocol (type: Standards). It should clearly explain why the existing protocol specification is inadequate to address the problem that the MIP solves. MIP submissions without sufficient motivation may be rejected outright.
- Specification - The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Mercury platforms, [and others](https://github.com/libertaria-project/wiki/wiki/Clients).
- Rationale - The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.
- Backwards Compatibility - All MIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The MIP must explain how the author proposes to deal with these incompatibilities. MIP submissions without a sufficient backwards compatibility treatise may be rejected outright.
- Test Cases - Test cases for an implementation are mandatory for MIPs that are affecting consensus changes. Other MIPs can choose to include links to test cases if applicable.
- Implementations - The implementations must be completed before any MIP is given status “Final”, but it need not be completed before the MIP is merged as draft. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of “rough consensus and running code” is still useful when it comes to resolving many discussions of API details.
- Copyright Waiver - All MIPs must be in the public domain. See the bottom of this MIP for an example copyright waiver.

## MIP Formats and Templates

MIPs should be written in [markdown] format.
Image files should be included in a subdirectory of the `assets` folder for that MIP as follow: `assets/mip-X` (for mip **X**). When linking to an image in the MIP, use relative links such as `../assets/mip-X/image.png`.

## MIP Header Preamble

Each MIP must begin with an RFC 822 style header preamble, preceded and followed by three hyphens (`---`). The headers must appear in the following order. Headers marked with "*" are optional and are described below. All other headers are required.

` mip:` <MIP number> (this is determined by the MIP editor)

` title:` <MIP title>

` author:` <a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s). Details are below.>

` * discussions-to:` <url>

` status:` <Draft | Vote | Accepted | Final | Active | Deferred | Rejected | Superseded>

`* review-period-end: YYYY-MM-DD

` type: `<Standards | Informational | Meta>

` * category:` <Core | Networking | Protocol | Schema>

` created:` <date created on, in ISO 8601 (yyyy-mm-dd) format>

` * requires:` <MIP number(s)>

` * replaces:` <MIP number(s)>

` * superseded-by:` <MIP number(s)>

#### Author header

The author header optionally lists the names, email addresses or usernames of the authors/owners of the MIP. Those who prefer anonymity may use a username only, or a first name and a username. The format of the author header value must be:

Random J. User &lt;address@dom.ain&gt;

or

Random J. User (@username)

if the email address or GitHub username is included, and

Random J. User

if the email address is not given.

While an MIP is a draft, a discussions-to header will indicate the mailing list or URL where the MIP is being discussed. As mentioned above, examples for places to discuss your MIP include [Mercury topics on Gitter](https://gitter.im/libertaria-project/topics), an issue in this repo or in a fork of this repo and [Reddit r/ethereum](https://www.reddit.com/r/libertaria-project/). No discussions-to header is necessary if the MIP is being discussed privately with the author.

The type header specifies the type of MIP: Standards Track, Meta, or Informational. If the track is Standards please include the subcategory (core, networking, protocol, or schema).

The category header specifies the MIP's category. This is required for standards-track MIPs only.

The created header records the date that the MIP was assigned a number. Both headers should be in yyyy-mm-dd format, e.g. 2001-08-14.

MIPs may have a requires header, indicating the MIP numbers that this MIP depends on.

MIPs may also have a superseded-by header indicating that an MIP has been rendered obsolete by a later document; the value is the number of the MIP that replaces the current document. The newer MIP must have a Replaces header containing the number of the MIP that it rendered obsolete.

Headers that permit lists must separate elements with commas.

## Auxiliary Files

MIPs may include auxiliary files such as diagrams. Such files must be named MIP-XXXX-Y.ext, where “XXXX” is the MIP number, “Y” is a serial number (starting at 1), and “ext” is replaced by the actual file extension (e.g. “png”).

## Transferring MIP Ownership

It occasionally becomes necessary to transfer ownership of MIPs to a new champion. In general, we'd like to retain the original author as a co-author of the transferred MIP, but that's really up to the original author. A good reason to transfer ownership is because the original author no longer has the time or interest in updating it or following through with the MIP process, or has fallen off the face of the 'net (i.e. is unreachable or isn't responding to email). A bad reason to transfer ownership is because you don't agree with the direction of the MIP. We try to build consensus around an MIP, but if that's not possible, you can always submit a competing MIP.

If you are interested in assuming ownership of an MIP, send a message asking to take over, addressed to both the original author and the MIP editor. If the original author doesn't respond to email in a timely manner, the MIP editor will make a unilateral decision (it's not like such decisions can't be reversed :)).

## MIP Editors

The current MIP editors are

` * Wigy (@wigy-opensource-developer)`

` * Bartmoss (@izolyomi)`

` * Byron (@alchebyte)`

## MIP Editor Responsibilities

For each new MIP that comes in, an editor does the following:

- Read the MIP to check if it is ready: sound and complete. The ideas must make technical sense, even if they don't seem likely to get to final status.
- The title should accurately describe the content.
- Check the MIP for language (spelling, grammar, sentence structure, etc.), markup (Github flavored Markdown), code style

If the MIP isn't ready, the editor will send it back to the author for revision, with specific instructions.

Once the MIP is ready for the repository, the MIP editor will:

- Assign an MIP number (generally the PR number or, if preferred by the author, the Issue # if there was discussion in the Issues section of this repository about this MIP)

- Ensure the PR includes a commit to move the MIP to the right name/folder (MIPs/mip-n.md) for accepted MIPs

- Merge the corresponding pull request

- Send a message back to the MIP author with the next step.

Many MIPs are written and maintained by developers with write access to the Mercury codebase. The MIP editors monitor MIP changes, and correct any structure, grammar, spelling, or markup mistakes we see.

The editors don't pass judgment on MIPs. We merely do the administrative & editorial part.

## History

This document was derived heavily from [Ethereum's MIP1] written by Martin Becze, Hudson Jameson and others which in turn was derived from [Bitcoin's BIP-0001] written by Amir Taaki which in turn was derived from [Python's PEP-0001]. In many places text was simply copied and modified.

### Bibliography

[Ethereum's MIP1]: https://github.com/libertaria-project/MIPs/blob/master/MIPS/mip-1.md
[Bitcoin's BIP-0001]: https://github.com/bitcoin/bips
[Python's PEP-0001]: https://www.python.org/dev/peps/

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).