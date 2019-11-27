# What is Servirtium?

Servirtium aims to be **a lingua franca for mock HTTP conversations using Markdown under source-control** 

* for test-automation purposes only
* with interoperable record and playback capability in Java, C#, Ruby, Python, NodeJS, Go, Rust and more.
* API makers would get on board too, shipping markdown conversations for known test scenarios
* oh, and JSON/YAML is the **wrong** format for encoding HTTP conversations in!

In common with other "Service Virtualization" technologies, tests leveraging previously recorded HTTP conversations:

* are much faster (real services vary in speed and can be slow)
* don't fail in unexpected ways (real services can be flaky)
* don't require credentials per person running the tests (real services often require API keys/tokens)

With Servirtium, the HTTP conversations for services invoked by running tests 
would be recorded and played back from the same Markdown format. Multiple language implementations would be able work with the same Markdown 
standard, and it would be possible to record a HTTP conversation with (say) a Ruby library using Test::Unit and the play them back via a 
(say) Java library for JUnit/TestNG test-writing teams. That would be for the situation where the Ruby team was publishing an API and 
bundled unit tests with it, but the team consuming API was in a different org/department and was using Java instead of Ruby.

So this would be exclusively for test automation purposes. Production deployments would not use any Servirtium technologies - the **real** services would be hooked 
up there.

# Example of that Markdown format

Here's a screenshot of some raw markdown source:

![2019-10-30_0559](https://user-images.githubusercontent.com/82182/67832718-7092e380-fada-11e9-94a8-58dcc82810cb.png)

(non-screenshot actual source: [https://raw.githubusercontent.com/servirtium/README/master/example1.md](https://raw.githubusercontent.com/servirtium/README/master/example1.md))

Here's what GitHub (for one) renders that like:

![2019-10-30_0555](https://user-images.githubusercontent.com/82182/67832562-f2ced800-fad9-11e9-9bbf-8a366ad7c938.png)

That's the whole point of this format that's human-inspectable in raw form and that your code-portal renders in a pretty way too.
If your code portal is GitHub, then 'pretty' is true.  

(non-screenshot actual rendered file: [https://github.com/servirtium/README/blob/master/example1.md](https://github.com/servirtium/README/blob/master/example1.md))

... and you'd be storing that VCS as you would your automated tests.

## Markdown Syntax Explained

### Multiple Interactions Catered For

Each interaction is denoted via a **Level 2 Markdown Heading**. e.g. `## Interaction N: <METHOD> <PATH-FROM-ROOT>`

`N` starts as 0, and goes up depending on how many interactions there were in the conversation.

`&lt;METHOD>` is GET or POST (or any standard HTML or non standard method/verb name).
  
`&lt;PATH-FROM-ROOT>` is the path without the domain & port. e.g. /card/addTo.doIt  

### Request And Reply Details Per Interaction

Each interaction has four sections denoted by a **Level 3 Markdown headers*

1. The request headers going from the client to the HTTP server, denoted like so `### Request headers recorded for playback:`
2. The request body going from the client to the HTTP server (if applicable - GET does not use this), denoted like so `### Request body recorded for playback (<MIME-TYPE>):`. And `&lt;MIME-TYPE>` is something like `application/json` 
3. The response headers coming back from the HTTP server to the client, denoted like so `### Response headers recorded for playback:`
4. The response body coming back from the HTTP server to the client (some HTTP methods do not use this), denoted like so `### Response body recorded for playback (<STATUS-CODE>: <MIME-TYPE>):`

Within each of those there is a single Markdown code block (three back-tick sequences) with the details of each.  The lines in that 
block may be reformatted depending on the settings of the recorder. If binary, then there is a Base64 
sequence instead (admittedly not so pretty on the eye).

# Recording vs Playback

## Recording a HTTP conversation

You'll write your test (say JUnit) and that will use a library (that your company may have written or be from a vendor). For 
recording you will swap the real service URL for one running a Servirtium middle-man server (which itself will delegate to 
the real service).  If that service is flaky - keep re-running the test manually until the service is non-flaky, and commit 
that Servirtium-style markdown to source-control.  Best practice is to configure the same test two have two modes of 
operation: 'direct' and 'recording' modes.

## Playback can fail too

Playback itself should fail if the headers or body sent by the client to the real service (through the Servirtium library)
are **not** the same versus the recording. It is possible that masking/redacting and general manipulations should happen
deliberately during the recording to get rid of transient aspects that are not helpful in playback situations.  The test
failing in this situation is deliberate - you're using this to guard against potential incompatibilities. 

For example any dates in headers of the body that go from the client to the HTTP Server could be swapped for some date 
in the future like "2099-01-01" or a date in the past "1970-01-01". 

The person who's designing the tests 
that recording or playback would work on the redactions/masking towards an "always passing" outcome, with no differences 
in the markdown regardless of the number of time the same test is re-recorded. 

Note: How a difference in request-header or request-body expectation is 
logged in the test output needs to be part of the deliberate design of the tests themselves. This is easier said than 
done, and you can't catch assertion failures over HTTP.

Note2: this is a third mode of operation for the same test as in "Recording a HTTP conversation" above - "playback" 
mode meaning you have three modes of operation all in all.

# Language Implementations

1. Java - [Servirtium-Java](https://github.com/servirtium/servirtium-java) (in this org) - ready to use
2. Python - [Servirtium-Python](https://github.com/servirtium/servirtium-python) Being developed story by story as part of demo [demo-python-climate-data-tck](https://github.com/servirtium/demo-python-climate-data-tck) and will later be extracted to the library/repo
3. Ruby - [Servirtium-Ruby](https://github.com/servirtium/servirtium-ruby) People wishing to lead development sought.
4. Go - People wishing to lead development sought.
5. C# - People wishing to lead development sought.
6. Node.JS - People wishing to lead development sought.

If you want to start one of these then read [starting a new implementation](starting-a-new-implementation.md)

We're also looking to existing *Service Virtualization* frameworks/libs to support (and help refine) the same Markdown format.

# Demo Projects

1. Java - [demo-java-climate-data-tck](https://github.com/servirtium/demo-java-climate-data-tck)
2. Python - [demo-python-climate-data-tck](https://github.com/servirtium/demo-python-climate-data-tck)
3. Ruby - TODO
4. .NET - TODO
5. NodeJS - TODO
6. Go - TODO

The Java project links to a couple of videos in use.  One is a general talk. Another is for a situation where the 
depended-on web API was flaky, and you get to see the real benefit of the tech.