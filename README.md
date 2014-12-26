XMLUnit 2.x
===========

XMLUnit is a library that supports testing XML output in several ways.

XMLUnit 2.x is a complete rewrite of XMLUnit and actually doesn't
share any code with XMLUnit for Java 1.x.

Some goals for XMLUnit 2.x:

* create .NET and Java versions that are compatible in design while
  trying to be idiomatic for each platform
* remove all static configuration (the old XMLUnit class setter methods)
* focus on the parts that are useful for testing
  - XPath
  - (Schema) validation
  - comparisons
* be independent of any test framework

This will be a work in progress for quite some time.  We are in the
process of migrating the - unpublished so far - XMLUnit 2.x from
sourceforge to github.  XMLUnit 1.x for Java and 0.x for .NET will
stay at [sourceforge](https://sourceforge.net/projects/xmlunit/).

## Buildstatus

XMLUnit 2.x for Java: [![Build Status XMLUnit 2.x for Java](https://travis-ci.org/xmlunit/xmlunit.svg?branch=master)](https://travis-ci.org/xmlunit/xmlunit)

## Help Wanted!

If you are looking for soemthing to work on, we've compiled a
[list](HELP_WANTED.md) of things that should be done before XMLUnit
2.0 can be released.

Please see the (contributing guide)[CONTRIBUTING.md] for details on
how to contribute.

## Examples

These are some really small examples, more is to come in the [user guide](https://github.com/xmlunit/user-guide/wiki)

### Comparing Two Documents

```java
Source control = Input.fromFile("test-data/good.xml").build();
Source test = Input.fromMemory(createTestDocument()).build();
AbstractDifferenceEngine diff = new DOMDifferenceEngine();
diff.addDifferenceListener(new ComparisonListener() {
        public void comparisonPerformed(Comparison comparison, ComparisonResult outcome) {
            Assert.fail("found a difference: " + comparison);
        }
    });
diff.compare(control, test);
```

or

```csharp
ISource control = Input.FromFile("test-data/good.xml").Build();
ISource test = Input.FromMemory(CreateTestDocument()).Build();
AbstractDifferenceEngine diff = new DOMDifferenceEngine();
diff.DifferenceListener += (comparison, outcome) => {
            Assert.Fail("found a difference: {}", comparison);
        };
diff.Compare(control, test);
```

### Asserting an XPath Value

```java
Source source = Input.fromMemory("<foo>bar</foo>").build();
XPathEngine xpath = new JAXPXPathEngine();
Iterable<Node> allMatches = xpath.selectNodes("/foo", source);
String content = xpath.evaluate("/foo/text()", source);
```

or

```csharp
ISource source = Input.FromMemory("<foo>bar</foo>").Build();
IXPathEngine xpath = new XPathEngine();
IEnumerable<XmlNode> allMatches = xpath.SelectNodes("/foo", source);
string content = xpath.evaluate("/foo/text()", source);
```

### Validating a Document Against an XML Schema

```java
Validator v = Validator.forLanguage(Languages.W3C_XML_SCHEMA_NS_URI);
v.setSchemaSources(Input.fromUri("http://example.com/some.xsd").build(),
                   Input.fromFile("local.xsd").build());
ValidationResult result = v.validateInstance(Input.fromDocument(createDocument()).build());
boolean valid = result.isValid();
Iterable<ValidationProblem> problems = result.getProblems();
```

or

```java
Validator v = Validator.ForLanguage(Languages.W3C_XML_SCHEMA_NS_URI);
v.SchemaSources = new ISource[] {
        Input.FromUri("http://example.com/some.xsd").Build(),
        Input.FromFile("local.xsd").Build()
    };
ValidationResult result = v.ValidateInstance(Input.FromDocument(CreateDocument()).Build());
bool valid = result.Valid;
IEnumerable<ValidationProblem> problems = result.Problems;
```

## Requirements

XMLUnit requires Java6 or .NET 3.5 respectively (it is known to work
and actually is developed on Mono 4).

The `core` library of each platform provides all functionality needed
to test XML output and haven't got any dependencies.  They use JUnit
4.x and NUnit 2.x for their own tests.

The core libraries are complemented by Hamcrest matchers and NUnit
constraints respectively.  For Java there also exists a `legacy`
project that provides the API of XMLUnit 1.x on top of the 2.x core
library.

## Building

### Java

XMLUnit 2.x builds using Ant, run `ant -projecthelp` for the available
targets, but mainly you want to runant

```sh
$ ant
```

in order to compile `core`, `matchers` and `legacy` and run the
tests.

```sh
$ ant jar
```

creates the corresponding jar files.

### .NET

XMLUnit 2.x builds using NAnt, run `ant -projecthelp` for the available
targets, but mainly you want to runant

```sh
$ nant
```

in order to compile `core` and `constraints` and build the assemblies.

```sh
$ nant test
```

executes the NUnit tests.