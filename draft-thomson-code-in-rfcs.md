%%%
    title = "The Use of Source Code in IETF RFCs"
    abbrev = "Code in RFCs"
    category = "bcp"
    docName = "draft-thomson-code-in-rfcs-latest"
    ipr = "trust200902"

    [pi]
    symrefs = "yes"
    sortrefs = "yes"
    toc = "yes"

    [[author]]
    initials = "M."
    surname = "Thomson"
    fullname = "Martin Thomson"
    organization = "Mozilla"
      [author.address]
      email = "martin.thomson@gmail.com"

%%%

.# Abstract

This document explores the problem of how to create, maintain, and publish
documents in the RFC series when those documents contain source code.

{mainmatter}

# Introduction

The IETF publishes technical specifications.  Some of those specifications
contain what could be considered source code.  This ranges from textual
descriptions of algorithms (e.g., [@RFC5280]) or pseudocode (e.g., [@RFC7541])
to functional code snippets (e.g., [@RFC1321]) complete functional
implementations (e.g., [@RFC6716]).

The IETF publishes documents that include artifacts that function much like
source code.  This includes YANG modules, XML schema, and ASN.1.  These
artifacts might be captured in IANA registries (e.g.,
https://www.iana.org/assignments/xml-registry/xml-registry.xhtml#schema).

The development of an RFC that contains source code, or code-like constructs
does not pose a particular challenge.  Authors of an internet-draft are able to
maintain their code using whatever practices they prefer, which might include
revision control, continuous integration, issue tracking, and other tools that
help ensure that the code meets a high standard.

However, once integrated into an RFC, there is an expectation that the code is
no longer updated.  Given that the only perfect code does absolutely nothing,
this presents a problem.

How should the IETF publish source code?  How does the IETF maintain the source
code it produces?

# The Errata process

The RFC errata process
[https://www.ietf.org/iesg/statement/errata-processing.html] provides a way for
users of an RFC to identify and share details of errors in specifications.
This process could be used for errors in source code components in a published
RFC, but the process could become untenable for source code.

Firstly, source code requires constant maintenance.  While the source code in
an RFC might be deemed correct at the time of publication, application of the
same source code to new problems, or in new environments can reveal flaws.  The
volume of such changes might be such that the errata system could become
overloaded. Note that the IESG are responsible for maintaining and running this
process.

For reference, since Opus was published in September 2012 as RFC 6716, there
have been approximately 1098 commits to their repository.  Note that this is
approximate, since I couldn't (easily) determine the git hash corresponding to
the code in RFC 6716; which is maybe indicative of a separate problem.

# Updating RFCs

The ECRIT working group published RFC 7852 [@RFC7582], during which they
discovered a bug in the schema in RFC 6351 (xCard) [@RFC6351] that would have
prevented them from making the changes they needed. RFC 6351 neglected to
include an extension point necessary for compatibility with the vCard RFC 6350
[@RFC6350].  This presented a difficult problem that required lengthy
discussion about the nature of the break with existing implementations the
working group would choose: break implementations that used the schema, or
break those that used the extension point.

The result was that a new RFC was required that replaced the IANA registry
entry established in RFC 6351. (Side note: RFC 7852 created this entry, but did
not update RFC 6351; not sure if that's a bug.)

The realization from this experience is that updating registries, particularly
those established by standards track documents, is a heavyweight process.
However it also demonstrated that the IETF is willing to update the code it
ships.

# Other Problems

Use of source code in the RFC series presents other challenges.

## Source Code Extraction and Use

It is difficult to extract information from the textual RFC format without
risking degradation.  RFC 6716 is careful to include instructions, a script,
and methods for verifying that the script was successful.  This is justified in
that case, but at almost a page, those instructions add a considerable amount
of overhead.

The new RFC format would provide a way to ensure better fidelity for source
code, though the XML format is naturally anathema to things like white space.
Assuming moderate care, code could be extracted from the XML form without any
loss of fidelity.

This drives a particularly important consideration: ease of use.  If the intent
of working groups is to develop code that is used by their community, then
making code machine accessible is important.

## Testing

Code that isn't tested to be fit for purpose won't be.  Paths that are tested
will be usable, paths that are not tested cannot be relied upon.

The purpose of RFCs is generally document the interoperability requirements for
software.  Including test suites for the source code that is included in an RFC
is arguably highly valuable, but it can be difficult.  An implementation of a
specification that has even moderate complexity will probably depend on a vast
web of dependencies that would make publication in RFC form unmanageable.

Including pieces of source code might be feasible given some assumptions about
its supporting infrastructure, but including tests that run depends on having
all of that infrastructure in place.

Simple building blocks are best: RFC 1321 has a test suite.  Complex tasks
rarely include tests: few XML schemas I have seen include test suites, or the
tests they have are superficial at best.

Test vectors are common in cryptography drafts, but extraction of these is
(again) left as an exercise for the reader.

## Archival

We have no good information on whether that code extracted from an RFC would be
interoperable - or even properly functional - years from now.  Will we have the
tool chain (hardware, operating system, compiler, libraries) necessary to build
and run that software?

Just from the Opus changelog, it's clear that compatibility problems remain a
constant burden. There are several mentions of C90 and C89 compilers, even C99
compilers.  Even a language as old and stable as C continues to evolve.

# Alternatives for Maintaining Source Code

It is entirely possible that we could continue to build and deploy software in
RFCs as we do today.  The process for managing updates could be iteratively
improved to make errata processing smoother.  The process for including source
code could be refined to make use, testing, and archival more tractable.

Current practice remains ad hoc.  The community might choose to continue this
pattern while we learn more.  Or we might collectively choose to identify and
document preferred practices and create guidelines around the use of source
code in the RFC series.

The reminder of this section describes areas that might be explored for
solutions.

## Maintain Source Code Independently

The Opus RFC contains the means for its own obsolescence: Appendix A.2 contains
a reference to the xiph.org git repository, where work continues on improving
the codec.  This is where Opus is built and maintained.

This means that the code that is in use is not the code that was archived in
the RFC series.  This might be appropriate where the value of the RFC is
derived primarily from its prose, but where source code augments or supplements
that text.

Archival requirements might be met either copying the source code - or critical
sections of it - to the RFC.  Alternatively, or in addition to this, the
location of the source code repository and a specific revision might be
identified.

Source code can be critical to the definition of the RFC.  For example, YANG
modules are heavily dependent on the code that comprises the module.  Failing
to include a YANG modules definition in an RFC would make the document
unusable.  However, it might be reasonable to include a reference to an
external source repository with a normative reference.

## Define "IETF" source code repositories

A source code repository that is separate from an RFC does not necessarily need
to remain outside of IETF change control.

The IANA currently maintains a source code repository.  However, the change
control process involves considerable manual effort: the degree depending on
the registration policy. 

The IETF (or the IANA) could maintain a source control repository for source
code. Modern source code repositories have good access control systems,
allowing for control over where change proposals come from, and how they are
accepted. Experts, working group chairs, or the IESG could be given
responsibility for particular repositories.  These systems also allow for
integration into continuous integration systems, allowing for tests to be run.

One challenge here is in managing forks.  Authors of an RFC might be unwilling
to cede control over their source code.  Thus, the process for making a change
would need to be well-defined, and have low enough friction that the incentive
to create an independent fork is reduced.


# Security Considerations

Almost certainly.


#  IANA Considerations

Maybe.


{backmatter}

# Acknowledgments

TBD.
