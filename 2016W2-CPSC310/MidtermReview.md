# Midterm Review

- [Course Intro](#course-intro)
  * [Software](#software)
  * [Software failures vs. defects](#software-failures-vs-defects)
  * [Software Engineering](#software-engineering)
  * [Programming vs. Software Engineering](#programming-vs-software-engineering)
- [Programming Languages](#programming-languages)
  * [Static vs. Dynamic](#static-vs-dynamic)
  * [Static vs. Dynamic Types](#static-vs-dynamic-types)
  * [Interpreted vs. Compiled](#interpreted-vs-compiled)
- [Asynchronicity](#asynchronicity)
  * [Callback shortcomings](#callback-shortcomings)
  * [Promises](#promises)
- [Testing](#testing)
  * [Testing Cycle](#testing-cycle)
  * [Terminology](#terminology)
  * [Levels of test](#levels-of-test)
  * [Testability](#testability)
  * [Downside of Testing](#downside-of-testing)
  * [Assertions](#assertions)
  * [Chai's Expect/Should Assertions](#chai-s-expect-should-assertions)
  * [Coverage](#coverage)
- [Teamwork](#teamwork)
- [Automation](#automation)
- [API](#api)
  * [Design Principles](#design-principles)
- [REST](#rest)
  * [Verbs](#verbs)
  * [Statelessness](#statelessness)
  * [Design Goals](#design-goals)
- [Specifications](#specifications)
  * [User Stories](#user-stories)
- [Software Processes](#software-processes)
  * [Waterfall software development model](#waterfall-software-development-model)
  * [Spiral model](#spiral-model)
  * [Agile approaches](#agile-approaches)
- [Abstraction](#abstraction)
  * [Data Abstraction](#data-abstraction)
  * [Control Abstraction](#control-abstraction)
  * [Decomposition](#decomposition)
  * [Information Hiding](#information-hiding)
- [Technical Representations](#technical-representations)
  * [UML Class Diagrams](#uml-class-diagrams)
  * [Deployment Diagrams](#deployment-diagrams)
  * [Sequence Diagram](#sequence-diagram)
  * [State Machine Diagrams](#state-machine-diagrams)
- [Information Security](#information-security)
  * [Security Principles](#security-principles)
  * [Mediation Strategies](#mediation-strategies)


## Course Intro

### Software

- Software is comprised of a series of instructions for a computer to execute that consume input, perform computation, and emit output.

### Software failures vs. defects

Failures

- Problems that cause a program to crash or require you to reboot your computer

Defects

- The program continues to execute but behaves incorrectly

### Software Engineering

> The process of transforming a mental plan of desired actions for a computer into a representation that can be understood by the computer. [Jean-Michel Hoc and Anh Nguyen-Xuan]

> The establishment and application of scientific, economic, social, and practical knowledge in order to specify, invent, design, build, validate, deploy, maintain, research, and improve software that is correct, reliable, and works efficiently on real machines.

This definition captures:

- the range of input skills required to build software (scientific, economic, social, and practical knowledge)
- the tasks performed by software engineers themselves (specify, invent, design, build, validate, deploy, maintain, research, and improve)
- the goals of the process (to build correct, reliable, and efficient systems)

### Programming vs. Software Engineering

- Programming only captures the 'build' task that is only a small piece of the spectrum of software engineering tasks. 
- The tasks that come before (like figuring out what to build and how to do it) and after programming (like deploying, maintaining, and evolving an existing system) are often crucial for ensuring that programming effort is effectively applied.



## Programming Languages

Programming languages define the vocabulary used to encode our ideas about what a computer should do in a format that a computer can understand.

Two goals:

1. To provide a format that is understandable to humans as they encode their intent for the desired behaviour of a program; and
2. To provide a format that can be unambiguously understood by automated tools enabling the language to be transformed into more compact executable representations. 


### Static vs. Dynamic

The true dynamic behaviour of a program is an aggregation of *all* program states across all executions. 

### Static vs. Dynamic Types

In a statically typed language:

- *variables* are given types and 
- a compiler checks that assignments to and usages of these variables are do not violate the type declaration

In a dynamically typed language:

- *values* rather than variables have types
- types are enforced by a compiler that checks source code to ensure that variables are only used in type-compatible ways

### Interpreted vs. Compiled

An interpreted language

- executes the code by starting at the top of the code listing and executing successive lines

```
// this will work because m1 is 
// declared before it is called

declare m1() {
   print 'in m1'
}

m1();
```

```
// this will not work because m1 is
// not declared before it is called

m1();

declare m1() {
   print 'in m1'
}
```

An compiled language

- First analyzed and transformed by a compiler before it can be executed
- This transformation can change the source code directly to machine code, or may emit some form of bytecode. This helps:
  - The code to be analyzed for syntax problems before runtime
  - Type checking (if the language is statically typed)
  - The emitted code to be optimized for runtime performance improvements
  - The code to reference elements that have not yet been defined (as the whole code can be analyzed in multiple passes by the compiler)



## Asynchronicity

JavaScript:

- can only execute in one thread a thread pool does exist in both Node and the browser to ensure that long running tasks can be offloaded in a way that does not block the main thread.
- functions execute on the call stack, and they always execute to completion
- only one method can execute at a time and another cannot run until the first finishes, it is important that JavaScript functions do not take 'too long' to execute
  - in JavaScript the most commonly-held measure is 16ms; exceeding this value will cause the UI to miss a refresh on a 60Hz monitor
  - Stuttering caused by computations exceeding these thresholds are referred to as '[jank](http://www.html5rocks.com/en/tutorials/speed/rendering/)' within the browser.
- JavaScript has a thread pool that has limited capabilities but is ideal for handling blocking operations (e.g., network and file IO, long-running tasks, timeouts, etc.). Rather than executing on the main thread, these execute in a separate thread pool and are returned to the call stack via callbacks when the event loop is idle.

### Callback shortcomings

Two common problems:

- handling errors is difficult because callbacks are invoked but they do not *return* a value. This means any information about the execution of the function making the callback must take place in the parameters.

  ```javascript
  fs.readFile('/cpsc310.csv', function(err, data) {
      if (err) {
         // handle
         return;
      }
      console.log(data);
  });
  ```

  It is only by convention that `err` is passed first; this depends on the developer doing the right thing and on clients checking `err` to see if it contains a value and acting appropriately if it has.

- This problem becomes harder to manage one callbacks depend on the output of previous callback functions

### Promises

Three states:

1. Pending: before it is done its task
2. Fulfilled: task has completed successfully
3. Rejected: task has completed erroneously

Two methods:

1. `then` is called when the promise is settled successfully
2. `catch` is called when the promise is settled with an error

These methods both themselves return promises enabling them to be chained.

```javascript
var file = null;
fs.readdir(source).then(function(files) {
    file = files[0];
    return fs.stat(file);
}).then(function(fileStat) {
    if (fileStat.isFile())
        return fs.readFile(file);
}).then(function(fileData) {
    // process data
}).catch(function(err) {
    // handle errors
});
```

Notes:

- Promises can only transition **to** *fulfilled* or *rejected* once and cannot change **between** *fulfilled* and *rejected*
- There can also be multiple `catch` statements
- When working with promises it is *strongly* encouraged that you end every `then` sequence with a `catch` even if you do not do any meaningful error handling
- Adding verbose debug messages can really help here so you can keep track of the order your functions and callbacks are executing

`Promise.all`

- `then` is called when *all* of the promise fulfill

`Promise.race`

- `then` is called when the *first* promise fulfills
- can be handy when there are multiple ways to accomplish a task but cannot know which will be fastest in advance, or
- if you want to show progress on a task by updating the user when data starts to become available rather than waiting for a full operation to be complete.



## Testing

The most prevalent quality validation approach in use today is automated testing (followed by code reviews).

> Program testing can be used to show the presence of bugs, but never to show their absence! [Edsger Dijkstra]

Why 100% test passing rate doesn *not* imply no bugs:

- Tests are programs that can have bugs too
- Specifications are often incomplete or imprecise

### Testing Cycle

1. Develop some new code.
2. Deploy to testing environment.
3. Run your tests.
4. Test fails.
5. Try to fix the failure through more development.
6. Deploy to testing environment.
7. Run the tests again.
8. Test passes.
9. Commit/push change.
10. Pull new changes.

Notes:

- 1-7 can occur many times before the code is ready to commit.
- In Test-Driven Development (TDD) the first develop step would be to write tests for features that haven't been written yet to guide future development

### Terminology

- `SUT/CUT`: System / code under test. This is the thing that you are actually trying to validate.
- `White box testing`: carefully examines the program source code in order to identify potentially problematic sets of inputs or control flow paths.
- `Black box testing`: 
  - Validate programs without any knowledge of how the system is implemented. 
  - Rely heavily on predicting problematic inputs by examining public API signatures and any available documentation for the CUT.
- `Effectiveness`: The simplest way to reason about the effectiveness of a test or test suite is to measure the probability the test will find a real fault (per unit of effort, which can be something like developer creation / maintenance time or number of test executions)
- `Higher/lower testability`: 
  - Some systems are significantly easier to test than others due to the way they are constructed. 
  - A highly testable system will enable more effective tests for the same cost than a system whose tests are largely ineffective (or require an outsized amount of creation and maintenance effort).
- `Repeatability`: The likelihood that running the same test twice under the same conditions will yield the same result.

### Levels of test

Range in size, complexity, execution duration, repeatability, along with how easy they are to write, maintain, and debug.

- `Unit`:
  - Unit tests exercise individual components, usually methods or functions, in isolation.
  - This kind of testing is usually quick to write and the tests incur low maintenance effort since they touch such small parts of the system. 
  - They typically ensure that the unit fulfills its contract making test failures more straightforward to understand.
- `Integration`:
  - Exercise groups of components to ensure that their contained units interact correctly together.
  - Touch much larger pieces of the system and are more prone to spurious failure.
  - Validate many different units in concert.
  - Difficult to identify the root-cause of a specific failure.
- `End-to-End/Acceptance`:
  - Tests typically validate entire customer flows.
  - Especially useful for validating quality attributes that cannot be captured in isolation such as performance and usability.
  - Great for ensuring that the system behaves correctly.
  - Provide little guidance for understanding why a failure arose or how it could be fixed.
- `A|B`:
  - Typically employed in production environments.
  - Two different versions are compared at runtime to validate which one performs 'best'.
  - Often used to validate business decisions, rather than evaluate functional correctness.
- `Smoke/Canary`:
  - A subset of a test suite that typically executes quickly, is highly reliable, and has high effectiveness.
  - Smoke tests try to expose a fault as quickly as possible, making it possible to defer running large swaths of unnecessary tests for a system that is known to be broken.
  - For example, if you have an error in your data model that touches your whole system, there is no need to run slow integration and end-to-end tests.

### Testability

- It is often necessary to restructure software that has not been written in a testable way to make it possible to validate a system effectively
- This commonly happens when units within the code take on more than one responsibility or when features become scattered across a codebase.
- One of the biggest advantages of Test-Driven Development (TDD) is that by writing your tests first you are able to ensure that your system is structured in a testable way.

4 high-level properties required for effective test writing and execution:

- `Controllability`: 
  - If the CUT cannot be programmatically controlled, an automated test cannot be written for it.
  - There is often a controllability tradeoff between what code it is *possible* to write a test for and what code it is *efficient* to write a test for. For example, if you build a large system that embeds all of its logic tightly with its interface it can be impractical to validate how the system works without writing UI-tests, which are error prone and expensive.
- `Observability`:
  - Sometimes the CUT can be invoked by a test but its outcome cannot be observed. For example, if a defective method mutates some external inaccessible object but does not return a value. Often these can be resolved by returning data to the callee that might otherwise just been passed further down a call chain.
  - One surprising challenge when considering observability is determining what values are correct and what values are erroneous. 
- `Isolateability`:
  - Being able to isolate the CUT under test is crucial to be able to quickly determine what has caused a failure so it can be resolved. This is challenging in large modern systems due to the number of (often third party) dependencies software systems have.
  - The most common approach to solving these is through simulation. In simulation-based environments code dependencies are *mocked* or *stubbed* whereby they are replaced with developer-created fake components that take known inputs and return known values. 
    - In this way the developer can test their code and ensure it handles specific cases correctly without fear that a bug in other parts may return an incorrect or inconsistent value.
  - Greatly increases performance and makes components less prone to non-determinism as the result being returned is usually fixed and not dependent on some external complex computation.
  - Make it possible to test program states that would otherwise be hard to trigger in practice.
- `Automatability`:
  - The above properties all aim to support making it possible to automate the execution of the test suite. 
  - Suites that can run without human intervention are much more likely to be executed than those that must be manually performed. 
  - They are also more likely to be run after a system has been released in the form of a regression suite to ensure that a system continues to execute as expected.
  - All systems exist within organizations, even if the software does not change, external dependencies, libraries, frameworks, and computing infrastructure will which will require continuous validation of even deployed systems.

### Downside of Testing

- Testing has a cost: tests are programs too and take time to write, debug, and execute and must also be evolved along with the system.
- Tests need to catch bugs. If all tests are passing then we still don't get anything valuable. This also relies on our trust that our test suite is capable of finding faults and *could* fail.

### Assertions

Assertions are the primary piece of machinery used to evaluate correctness within automated unit test suites.

2 high-level models to consider when structuring tests:

- Four-Phase Tests: The four phases correspond to
  1. setting up the testing environment
  2. executing the code under test
  3. evaluating the output of the CUT
  4. tearing down the testing environment
- Give-When-Then: This model was developed by the behavioural driven programming ([BDD](https://dannorth.net/introducing-bdd/)) community which strives to create 'executable specifications' through unit tests that use descriptive strategies for nesting and naming.
  - Tests are structured from some *given* state, where the system's configuration is understood. 
  - The key action of the test is the *when*; this is often described in the test name using plain language.
  - The *then* step involves observing the output to ensure its correctness.
  - Expect/Should-style assertions (such as those used by the [Chai library](http://chaijs.com/api/bdd/)) are also often used by the BDD community as they enable extremely descriptive assertion statements.

### Chai's Expect/Should Assertions

```javascript
expect('myName').to.equal('myName');
expect(1).not.to.equal(2);
expect(obj).to.deep.equal({ key: 'value' }); // check large object
expect(42).to.be.a('number'); // check type
expect([ 42, 97, 102 ]).to.have.length.above(1); // check array properties
expect([1, 2, 3]).not.to.include.members([1, 4]); // checks set membership

//readable
describe('Check math constants', function() {
    it('Math.PI should be close enough to the correct value', function() {
        expect(3.1415).to.be.closeTo(3.14, 0.025);
    });
});
```

It is often best to have each behaviour tested as independently as possible:

- Ease debugging because a change that breaks a behaviour will be easy to isolate as it will only have broken one tests and not a handful.
- Also ease program evolution because changing a behaviour will not require modifications to lots of different tests.

Three ways of breaking down the behaviours under test:

- Normal conditions: The normal conditions represent the way we expect to be used.
- Unexpected conditions: The unexpected conditions arise when the code is used in unexpected ways.
- Boundary conditions: All programs consume input; by testing boundary values we can ensure our program will successfully operate with the breadth of inputs the program might encounter.

Ultimately each test should compare an expected value against the actual value produced by the code under test. 

```javascript
describe('Check math constants', function() {
    it('Math.PI should be close enough to the correct value', function() {
         const expected = 3.1415;
         const actual = Math.PI;
        expect(expected).to.be.closeTo(actual, 0.025);
    });
});
```

### Coverage

At its core, all coverage tools measure the proportion of the system that is executed by the test suite.

2 properties:

- Relatively cheap to compute: simply needs to track which parts of a program have executed when the test suite is executing.
- Actionable: can just look at the result and decide how to act upon this information in a straightforward way.

Three types of coverages:

- Statement: the fraction of the statements of the code that are executed.
- Decision/Branch: checks to make sure that all branching options within the system are executed. Decision coverage only measures those program points where execution can take divergent paths within the system.
- Modified Condition (MCC): checks all program entry and exit points along with every combination of decisions within a program.

Example:

```javascript
1: eval(x: number, c1: boolean, c2: boolean): number {
2:  if (c1)
3:    x++;
4:  if (c2) 
5:    x--;
6:  return x;
7: }
```

Test 1:

```javascript
eval(0, false, false);
```

- 60% statement coverage (lines 2, 4, 6)
- 50% decision coverage (only the false branches of the decisions on lines 2 and 4) 
- 25% MCC coverage

Test suites:

```javascript
// 100% statement coverage
eval(0, true, true);

// 100% decision coverage
eval(0, true, true);
eval(0, false, false);

// 100% MCC coverage
eval(0, true, true);
eval(0, false, false);
eval(0, true, false);
eval(0, false, true);
```

Shortcoming:

While it clearly shows that the suite *executes* the code under test, it does not show that the code under test is *correct*. —> Ultimately we really do care about the correctness of the system over how comprehensively a test suite might execute it.



## Teamwork

Three coarse-grained levels of interpersonal maturity:

- Dependent: completely dependent on their teammates, not able to contribute effectively on their own, cannot be relied upon to effectively complete tasks
- Independent:  able to perform meaningful tasks on their own, intrinsically motivated: they are motivated by working autonomously, improving their skills, and accomplishing things on their own.
- Inderdependent: able to keep a global view of the needs and goals of the team, and its members, extrinsically motivated: they are motivated by seeing the team succeed as a whole.

High-level guidelines:

- Effective Communication
- Project Management: User stories and milestone plans are two common techniques for project management used by agile teams.
- Conflict Management: treating conflict as an opportunity for problem solving or give-and-take negotiation is more effective than withdraw (avoiding the problem), smoothing (pretending the problem doesn't exist), or forcing (unilaterally applying one solution) in practice.

Five team dysfunctions:

- Absencce of trust
- Fear of conflict
- Lack of commitment
- Avoidance of accountability
- Inattention to results



## Automation

Ultimately effective automation will allow a software system to be checked out, configured with the correct dependencies and tools, built, and unit tested with a single command (or a short sequence of commands that can be placed in a simple script).

The goal of automation is to transform this unsustainable way of building software into a process that is:

- Repeatable: A unified building process provides a mechanism for building the system the same way, every time with minimal (prefereably no) human intervention required.
- Reliable: Being able to trust that the repeatable process will run to its successful completion is crucial
- Revertible: The build process should make it possible to quickly and transparently revert out of any change.

Software building tasks that can be automated:

1. Source control: Every software team uses version control to track their source code resources (and other assets including any automation tools themselves). e.g. `git` and `hg`
2. Dependency management: Code is not developed in isolation; all systems are built upon a host of existing libraries and frameworks. Dependency management solutions provide a means to reliably procure software required for the build process. e.g. `ant`, `mvn`, `npm`, and `yarn`.
3. Build tools: Compiling the code into shippable units (be they libraries or actual executables) is the next step. While these tasks are sometimes easy, they often involve many independent steps that are joined together by build tools like `make`, `ant`, or `gulp`.
4. Test tools: Tests are code too, so enabling them to be written with as little boilerplate code as possible is important for reducing the costs of automated testing. e.g. `jUnit`, `NUnit`, `mocha`
5. Test runners: Once test suites have been developed they must be run in a repeatable way. While this can take place on the engineer's development machine, tests are often run on a common infrastructure to increase the consistency between test runs for all developers. Tools like `Jenkins`, `Bamboo`, and `TravisCI` all work to execute test suites remotely. These first five steps are often called ***continuous integration***.
6. Deployment: In the online realm software must be deployed once it is built - *continuous deployment*

## API

Application Programming Interfaces (API) describe the programmatic interfaces used for interacting with a software system.

- The primary purposes of APIs are to provide a predictable means of programmatic interaction and to hide implementation details from the API client.
- By hiding the internal implementation, APIs enable the API owner to change their underlying implementation without affecting any clients that may depend upon it.
- This is because services hosted through REST-based interfaces behind APIs give API owners an easy mechanism for maintaining and updating their systems and controlling billing without having to release the actual source code for their systems.
- It is important that software engineers carefully design their interfaces to make them easy to understand, use, maintain, secure, and extend.

### Design Principles

Goal:

APIs should be easy to use and hard to misuse.

High-level API design advice:

- APIs should do one thing, and do it well (also known as the single responsibility principle).
- APIs should be kept as small and simple as possible; once an API is released it is hard to remove.
- APIs should *never* expose internal implementation details; these make it difficult for the consumer to use and for the maintainer when they want to change an internal implementation detail.
- The 'usability' of a system to another engineer is dictated by the quality of the API. 
  - This means names should be well considered and consistent
  - The APIs, their constraints, and their pre- and post-conditions should be documented. 
  - Consistent APIs reduce developer surprise and helps them to naturally move from one API to the next.



## REST

- REST is an architectural style heavily used in web-based systems for providing services between different systems (and interfaces).
- REST defines a means of connecting systems together that focuses on their interfaces rather than their implementations using self-descriptive messages.
- In this way, REST provides a language to define a specific kind of APIs for communicating across a network. REST services are transported over the network using the HTTP protocol.
- REST services define their resources using uniform resource identifiers (URIs).
- URIs differ from URLs in that a URI does not define its network location and access mechanism (e.g., `/dataset` is a URI while `http://foo.com/dataset` is a URL).
- Additional data can be sent to a REST endpoint in the form of HTTP headers or in the HTTP body.
- It is idiomatic for headers to not contain any information about the location of the resource (this should be in the URI) or to carry any data about the request (this should be in the body); headers are mainly for sending metadata (e.g., to request the response data format or encoding or to provide authorization credentials).

### Verbs

- REST describes how HTTP verbs should be used when communicating with REST services.
- REST services respond with payloads (typically JSON or XML) and an integer-based response code.
- Properties of REST methods:

  - Idempotent: An HTTP method that can be called many times without different outcomes. It would not matter if the method is called only once, or ten times over. The result should be the same. Again, this only applies to the result, not the resource itself. 
- Each REST request uses only one verb and receives only one response.

  - `GET`: read a resource.
    - GET requests should never modify data on a server, meaning that calling them repeatedly should have no visible effects on the server
    - GET requests can have a body but this is not idiomatic
    - Requested resources are defined by the URI alone. 
    - Idempotent
    - Common response codes are 200 (ok) and 404 (not found).
  - `PUT`: update a resource at a known URI.
    - PUT can also be used to create a resource if the client is allowed to define a new URI that does not already exist.
    - Response bodies are not typically returned for these requests.
    - PUT modifies server resources but in the same way for every request and can be considered idempotent.
    - Common response codes: 200 (ok), 204 (no content), and 404 (not found).
  - `POST`: create a new resource.
    - The response body should contain a URI to the newly-created resource. 
    - Not idempotent: POST modifies server resources.
    - Common response codes: 201 (created), 404 (not found), and 409 (conflict).
  - `DELETE`: removes a resource from the server.
    -  Usually no response body
    -  Resources on the server will be unchanged so this verb is idempotent. 
    -  Common response codes: 200 (ok) or 404 (not found).
  - `PATCH`: modify (not completely replace or delete) a resource.
    - Not idempotent
    - Common response codes: 200 (ok), 204 (no content), 404 (not found).



REST services almost universally respond to requests with XML or JSON requests. 

- XML documents are more verbose, harder to read and write by hand, and are more difficult to parse.
- JSON documents are easier to read and write, are less verbose, and are easier to parse but are not amenable to comments which can make them harder to document (in a way that is still a valid JSON document).

### Statelessness

REST services are often viewed as highly scalable. The main reason for this is that there is an expectation that the client maintains state instead of the server. As a concrete example, if a client is trying to undertake a complex multi-step operation, it is the client's responsibility to keep track of where they are, not the server's responsibility.

### Design Goals

When designing REST APIs:

- API must provide a *valuable* service.
- Should also be sufficiently *flexible* to work in a wide variety of circumstances.
  - providing multiple data formats, supporting different protocols, or versions
  - enabling batch or update operations can also ease common developer tasks that might otherwise be hard to perform —> considering the bootstrap process and how long it takes a developer from visiting an API document to successfully making a query (often referred to as the time-to-hello-world)
- Effectively *measuring* your APIs —> status pages
- Effective API *documentation*
  - what the API does, what parameters it expects, and the structure of its return document. 



## Specifications

Specifications are documents used to describe a software system. They describe *what* a system is to do, but not *how* it is to be done.

Two broad categories of requirements:

- Functional Requirements: These describe what the system must *do* —> **describing the behavior of the system**
- Non-functional Requirements: These describe properties the system must *have* —> **elaborates a performance characteristic**

Four important properties:

- Complete: A spec that does not completely describe the requirements provides opportunities for misunderstanding between stakeholders.
- Consistent: Conflicting requirements also generate misunderstandings that can make knowing the *right* behaviour impossible to understand.
- Precise: Natural language is inherently imprecise while source code must be precise for the computer to be able to interpret it.
- Concise: More content —> more space for imprecision and inconsistency to become problems.

Requirements in addition to the functional and non-functional properties:

- Design Constraints: these can include regulatory compliance, budget, languages and frameworks, and development/security process requirements.
- Environmental Constraints: these constraints often the mandate execution environment (e.g., the operating system, library limitations, services that can be consumed and must be provided) and operational expectations (e.g., expected input, performance constraints, disaster recovery).
- Preferences: preferences provide a mechanism to explicitly rank requirements should they come in conflict.

Pipeline process:

Elicitation — Analysis — Reification — Validation

### User Stories

User stories are lightweight descriptors of features that are often used by engineers to specify software development tasks.

Format: 

1. Role - goal - benefit.
   - "As a `<stakeholder>` I want `<function>` so that `<value>`."
2. Notes: describes limitations and and provides any clarifications that might be necessary to further explain and scope the feature.
3. Definition of done: provides a specific description of how the story can be validated by both the developer and the product owner to ensure it is completed correctly.
4. Engineering notes
5. Estimate: Days (0.5 days to 5 days) or a notion of points (1/2 to ~40)

**INVEST**:

- **I**ndependent: Stories need to be independent and self-contained so they can be reordered and implemented as needed.
- **N**egotiable: Choosing which stories are performed in the next iteration needs to be a negotiable activity.
- **V**aluable: Being clear about the value of a story is important
- **E**stimable: If a story lacks sufficient detail to evaluate its feasibility (and how long it will take) more specification work is needed. 
- **S**mall: User stories tend to describe features that take between half a day and half an iteration in length. 
- **T**estable: The definition of done is fundamentally important so all stakeholders understand the ways in which the story will be evaluated. 

Example:

- As a *prof*, I want to be able to *create* a repository for a 310 team so they can *start* working on the project.
- Notes: Inputs: team name, initial repo, team member GitHub ids.
- Definition of done:
  - Single command will take params and complete task.
  - Success should be programmatically verifiable.
  - Unit tests to check for error handling for org, team name, and members.
  - Integration test will ensure compatibility with the GitHub API.
  - Script-based tests will be provided for the command line aspects of the feature.
- Engineering notes:
  - Must integrate with existing `GitHubManager`
  - Any constants that would need to be changed must be stored in `config.js`.
  - API will be used by user interface in future, keep this in mind when designing API.
- Estimate: 1.5 units.



## Software Processes

### Waterfall software development model

Requirements —> Design —> Implementation —> Verification —> Maintenance

- primary shortcoming of waterfall-based processes is that there is extreme pushback against revisiting a prior phase: once requirements are complete the requirements are fixed.
- not flexible in the face of organizational change.
- the overall value of the system cannot be validated until the process has run to completion —> problematic for long-running projects

### Spiral model

- Planning: Gathering and analyzing requirements.
- Risk Analysis: Identify sources of risk and mitigation strategies.
- Engineering: The system is built and validated.
- Evaluation: System is validated externally with customers to inform future iterations.

Pros:

- Compared to Waterfall, the spiral model enables much greater sensitivity to changes to requirements than waterfall. 
- by keeping customers involved, the Spiral model can avoid situations where teams end up building the wrong piece of software.

Cons:

- the overhead of performing effective risk analysis and review can be overwhelming
- the duration between spirals (often over one year) result in large delays between customer feedback.

### Agile approaches

Four main tenets:

- Individuals and interactions over processes and tools.
- Working software over comprehensive documentation.
- Customer collaboration over contract negotiation.
- Responding to change over following a plan.

Three methodologies:

- Extreme Programming (XP): systems should always be buildable and that developers should be willing to start small and adapt and refactor their systems as the need arose, rather than investing in huge up-front engineering efforts.
  - Communication
  - Simplicity: focusing on the simplest possible solution
  - Feedback
  - Courage
  - Respect
- Test-Driven Development (TDD): to ensure automated testing is always a key part of the development process. Important because agile approaches rely heavily on being able to quickly refactor a piece of code and refactoring is often risky if the end-state of a refactoring cannot be easily validated relative to the input state. In addition to validation, this also ensures that *clients* will have access to the necessary API to use the new feature effectively.
  - Add a test
  - Run the tests to ensure they fail
  - Write the code/run the tests
  - Refactor the code
- Scrum: most commonly-used agile process. An incremental iteration-based methodology that breaks work into fixed-length *sprints* (these range from one to three weeks). At the end of each sprint the code should be shippable, that is it is properly tested, but likely not complete. A product has a global set of requirements left to be implemented known as the *product backlog*. The work items being worked on in each sprint are called the *sprint backlog*. 
  - Process-specific roles:
    - Product owner: defines product features and helps to prioritize 	features according to their value to the team.
    - Scrum master: facilitates the scrum process, *not* manager of the team
    - Team: Scrum-based teams are typically diverse and cross-functional containing designers, managers, engineers, clients, and test specialists. Usually 5-9 people in total.
  - Three broad ceremonies:
    - Sprint planning: takes place before the sprint and is an opportunity for the product owner to describe and motivate the highest priority user stories from the product backlog for the upcoming sprint (that will then comprise the sprint backlog)
    - Standup meeting: teams meet daily for a *daily scrum* or *standup* meeting.
    - Sprint review: held for the team to demonstrate their features to the product owner or other stakeholders. Chance to evaluate how much progress has been made on the sprint backlog.
    - Retrospective: teams take time to reflect on how the sprint worked out.



## Abstraction

Abstraction enables engineers to focus on the *key* information for a given task while eliding unnecessary detail. The most high-level and common kinds of abstractions relate to control and data abstraction.

### Data Abstraction

The process of explicitly separating the abstract properties of a data type and its concrete implementation —> enables client code to be oblivious of the underlying implementation of a data type allowing it to be upgraded and improved without impacting client code.

### Control Abstraction

Abstracting away how the code will execute is called *control abstraction*. It allows an engineer to consider *what* they are doing without becoming overwhelmed with *how* they are going to do it.

### Decomposition

Decomposition is the process of taking a complex high-level entity and splitting it into more manageable smaller pieces.

- Top-down decomposition: a description is broken up into pieces starting at the top and working to greater level of detail. Working top-down is a great way to have global awareness about the full system but can be challenging once terminal boxes develop constraints that necessitate revisiting prior decisions.
- Bottom-up decomposition: decisions can be made about leaf nodes that can be composed into the final overall system. Often lack the global overview which can lead to inconsistencies and extraneous focusing on details early in the design stage.

### Information Hiding

Information hiding is a high-level motivation for APIs: by describing the expected behaviour of the API you can "hide" the implementation behind it.The most common language feature for supporting encapsulation is the interface whereby the interface describes the public contract and the concrete class describes the implementation (along with its supporting private methods and fields).

Encapsulation is concerned with delineating the contractual interface with its interface. 



## Technical Representations

- Representations can ensure all stakeholders have a consistent view of how the system will end up being built. 
- It is important that these representations are not ambiguous (open to more than one interpretation), are accurate (correct within some given tolerance), and are precise (so the correct system can be built repeatably from the same abstraction).
- Representations can also be used for understanding existing systems during maintenance activities.
- Each representation (diagram) can be considered a *view* of the system. 
  - Views often have specific goals, focusing on subsets of elements or relationships.
  - it is important to keep in mind that views typically overlap —> consistency —> potential problems of the system

Structural and behavioural diagrams:

- Structural diagrams capture the key elements and their relationships within the system
- Behavioural diagrams model how the system components interact with each other dynamically at runtime.

### UML Class Diagrams

- Static representations of the software elements and their relationships within a system. 
- For object-oriented systems class diagrams detail the key classes, fields, and methods within a system and how they relate to each other statically. 
- Class diagrams can be translated fairly directly into code skeletons (and vice versa).
- Syntax
  - Classes and interfaces are represented by simple rectangles that detail their names, fields, and methods.
  - public: +, protected: #, private: -
  - Large hollow arrows represent inheritance relationships.
  - Dotted lines with simple arrowheads represent dependencies (typically through method calls and field references)
  - Solid lines with filled diamonds represent composition relationships; these relationships indicate that the class on the diamond-end creates and is primarily responsible for the lifecycle of one ore more variables of the linked type.

### Deployment Diagrams

eployment diagrams overlay static class diagrams onto the execution containers and physical machines upon which they will reside. Simple deployment diagrams might only concentrate on mapping modules (or classes) onto machines, but in more sophisticated environments could include mapping the software into specific execution containers (e.g., virtual machines, container images, etc.).

### Sequence Diagram

### State Machine Diagrams



## Information Security

- Security comprises a huge space including physical security (e.g., key loggers), system security (e.g., denial of service), network security (e.g., man-in-the-middle), and human security (e.g., social engineering)
- Assets: the information or systems that is being secured.
- Subjects: the actors, both legitimate and otherwise, who are interacting with the system and data
- Policies: rules associated with the system
- Threats: violations of the policies that can be employed to break the security of the system.

Four high-level requirements:

- Confidentiality: Preventing unauthorized parties from accessing (or even knowing about) secured data.
- Integrity: Ensuring that only authorized parties can manipulate data, and only in an approved manner.
- Availability: Resources should always be accessible by authorized users on appropriate occasions.
- Accountability: It should be possible to know how subjects interact with sensitive systems and data.

Four high-level steps involved in securing systems:

- Understanding: understand the system and its context.
- Analysis: build meaningful threat models to understand how and why a subject would want to violate the system's security policies
- Mitigation: the work of actually securing the system —> involves reducing the amount of system surface available to unauthorized parties while maintaining authorized user access.
- Validation: testing and security reviews are crucial to check the mitigation steps have been successful.

### Security Principles

- **Defence in depth**: A small violation should not easily lead to a big one. This means that security should not only be implemented at the perimeter of the system and internal controls should still be present. Varying internal security measures can further help avoiding failure propagation. As with most security countermeasures this negatively impacts system complexity and can decrease system usability (as is true for many of these principles).
- **Least privilege**: Coarse-grained privileges allow both malicious and accidental access to unauthorized data. Implementing roles and access control lists are one of the most common ways of avoiding these exploits, although this only really works once the subjects, assets, and policies for the system has been carefully specified.
- **Separation of duties**: Sensitive or critical actions in a secure system should not be left to a single user. This applies to both authorized (to avoid accidents) and unauthorized parties (to avoid broad exploits). This kind of separation is often apparent in accounting systems whereby actions must be approved by second or third parties to ensure compliance and correctness.
- **Strong authentication**: Making it hard for unauthorized users to gain access to the system can be enhanced using strong passwords or some kind two-factor (or multi-factor) authentication. In two-factor authentication, users must both know something (like their password) and have something (like a phone, chip-with-pin card, or number-generating fob). This makes it so if only one part of the mechanism was lost (for instance the password written on a post-it note) the system could not be directly breached.
- **Non-repudiation**: System users must be held accountable. Non-repudiation means auditing system behaviour to track how the system is being used. Auditing is an essential tool for tracking the extent to which unauthorized parties have accessed the system and what they have done. It is also useful for ensuring that authorized parties know their usage of the system is being traced. One downside of this approach is that audit logs themselves can contain sensitive information (or point to it) and must themselves be carefully secured.

### Mediation Strategies

After a system has been modelled and its threats understood, we can start to design mitigation strategies. Rather than step through an abstract list of strategies, here we will discuss more concretely the kinds of steps Google undertakes. These are covered in a great [white paper](https://cloud.google.com/security/security-design/) if you want additional detail.

Google's security strategy revolves around providing a secure infrastructure for all of their services to use. These range from low-level hardware support to high-level operational support. Their mitigation strategies can be split into five key levels:

- Hardware infrastructure: In-house designs for server and network equipment to reduce risk of unknown hardware running in secure facilities.
- Service deployment: All services communicate using encrypted links. Services can only interact with other services with which they are authorized. Services can also only read/write data they are authorized to access. Services run in independent hypervisors although some critical services are also run on completely independent machines.
- User identity: All Google employees use multi-factor authentication internally. Employees are also granted restricted fine-grained access to they services and data they are allowed to access.
- Secure data storage: All data is encrypted with rotating key encryption. Data remanence (e.g., deletion) is also carefully managed, although there are challenges given the distributed nature of the data.
- Internet communication: All front-end services go through the GoogleFrontEnd (GFE) that ensures that perfect forward security is correctly applied and that all certificates are correctly configured. The GFE also has the ability to mitigate DoS attacks by throttling or balancing connections.
- Operational security: Engineers practice safe coding practices (such as security code reviews, using static and dynamic analyses, and employing security-aware frameworks and libraries). Google also works with the OSS projects that its tech stack relies on to find vulnerabilities in code they are using that could compromise their own services.

At their core, these mitigation strategies are a great example of defence in depth (multiple layers of countermeasures that does not solely rely on border security), least privilege (employees are strictly limited in the services and data they can access, as are the services themselves from one another), strong authentication (universal 2FA authentication that not only grants access to systems but is passed along with individual service calls), and non-repudiation (extensive logging and unusual activity detection).
