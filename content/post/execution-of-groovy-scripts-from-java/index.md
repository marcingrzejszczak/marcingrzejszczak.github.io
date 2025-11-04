---
title: "Execution Of Groovy Scripts From Java"
summary: "we can see that there is no groovy plugin - it has been done deliberately since we don't want our scripts to be compiled. Now let's take a look at the logic behind the TransformerFactory that compiles the Groovy script."
date: 2013-03-30

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - admin

tags:
  - Blog

content_meta:
  trending: false
---
## Problem with mappings
In our project we came across a really big problem related to mapping. Having two systems that initially were defined by the BA to be somehwat simillar we have chosen the simple XSLT (done through Altova Mapforce) of the input message to the output one.
Afterwards it turned out that the functions required to perform a mapping are becoming enormous. An example of such a mapping is:
> From the input message take a list of Cars iterate over it and find a Car whose field "prodcutionDate" is the lowest and the attribute "make" is equal to "Honda" and as the output return the "saleDate"
So in order to map it we decided to move to JBoss Drools. The
[decision tables](https://toomuchcoding.com/2013/02/drools-decision-tables-with-camel-and.html)
were out of question since the logic was to complex and customized to be placed in the spreadsheet so we coded everything in the DRL files. Soon the rules got really big and some of our developers were forced to spend plenty of time on constant recreation of rules stated by the BA.
Out of frustration and after having seen all the amazing things at the
[33rd degree conference](https://2013.33degree.org/)
I decided to start finding solutions to my problems which were:
[]()
1. The DRL files are big and started to become unmaintainable (for a single field we had for example 4 rules)
2. Since the BA has never coded a single Drools rule / XSLT  in his life adding a simple if... else... statement for him is not a problem
3. The BA has to wait for the mapping implementation by the devs until he can test it
4. The devs are spending far too much time on coding the mapping rules instead of developing other features
After stating these problems a research regarding mapping frameworks took place and one of the concepts that I began working on was trying to create the mapping in Groovy. Since Groovy (thanks to for example PropertyMissing and MethodMissing) is a perfect language for creating a DSL I decided to start right away. The only two things I had to remember about were:
1. The current application is written purely in Java
2. The mapping code (in order to perform fast testing) has to be detached from the application as such - it can't be compiled during deployment because we want to have the possibility of frequent substitutions of the mappings
## Project structure
Having defined the language, the constraints I created the following solution:
[![](https://2.bp.blogspot.com/-LUqaZQkqom4/UVdMZkiW7RI/AAAAAAAABDc/f3sRJ9fF6AM/s320/Project+structure.png)](https://2.bp.blogspot.com/-LUqaZQkqom4/UVdMZkiW7RI/AAAAAAAABDc/f3sRJ9fF6AM/s1600/Project+structure.png)
The project structure
As you can see the project structure is very simple. To begin with it is built in
**Gradle**
. The main function can be found in the
**XmlTransformer.java**
. The flow is such that the
**TransformerFactory**
creates a
**Transformer**
basing on the Groovy script that came out of the
**ScriptFactory **
(in our project for different types of products that we distinguish by a field in the XML file, we have different DRL files). The Groovy scripts are residing in the classpath in the
**/groovy/**
folder (of course at the end of the day those scripts should be placed outside any jars).
In the
**build.gradle**
```groovy
apply plugin: 'java'group = 'com.blogspot.toomuchcoding'version = '1.0'repositories {
  mavenCentral()
}
dependencies {
  compile 'org.codehaus.groovy: groovy-all: 2.0.5'    compile 'org.slf4j: slf4j-log4j12: 1.7.2'    compile 'log4j: log4j: 1.2.16'    compile 'com.google.guava: guava: 14.0'    testCompile group: 'junit',
  name: 'junit',
  version: '4.+'
}
task(executeMain,
dependsOn: 'classes',
type: JavaExec) {
  main = 'com.blogspot.toomuchcoding.XmlTransformer'    classpath = sourceSets.main.runtimeClasspath
}
```
we can see that there is no groovy plugin - it has been done deliberately since we don't want our scripts to be compiled. Now let's take a look at the logic behind the TransformerFactory that compiles the Groovy script. What is really important is the fact that our Groovy class implements an interface created in our Java project - we want from the Java point of view to have no problems with execution of the Groovy code.
**TransformerFactoryImpl.java**
```java
package com.blogspot.toomuchcoding.factory;
import com.blogspot.toomuchcoding.transformer.Transformer;
import com.google.common.io.Resources;
import groovy.util.GroovyScriptEngine;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.io.File;
import java.net.URL;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 22.03.13 * Time: 23:54 */public class TransformerFactoryImpl implements TransformerFactory<String, String> {
    private static final String GROOVY_SCRIPTS_CLASSPATH = "groovy/";
private static Logger LOGGER = LoggerFactory.getLogger(TransformerFactoryImpl.class);
private ScriptFactory scriptFactory;
private GroovyScriptEngine groovyScriptEngine;
public TransformerFactoryImpl(ScriptFactory scriptFactory) {
        this.scriptFactory = scriptFactory;
try {
            groovyScriptEngine = new GroovyScriptEngine(GROOVY_SCRIPTS_CLASSPATH);
}
 catch (IOException e) {
            LOGGER.error("Exception occurred while trying to create the Groovy script engine", e);
throw new RuntimeException(e);
}

}
    @Override    public Transformer<String, String> createTransformer() {
        Transformer<String, String> transformerFromScript = null;
try {
            File scriptFile = scriptFactory.createScript();
URL scriptAsAClasspathResource = Resources.getResource(GROOVY_SCRIPTS_CLASSPATH + scriptFile.getName());
Class classFromScript = groovyScriptEngine.loadScriptByName(scriptAsAClasspathResource.getFile());
transformerFromScript = (Transformer<String, String>) classFromScript.newInstance();
}
 catch (Exception e) {
            LOGGER.error("Exception occurred while trying to execute Groovy script", e);
}
        return transformerFromScript;
}

}
```
A
**GroovyScriptEngine**
is used to load a script by name. I chose the
[GroovyScriptEngine](https://docs.codehaus.org/display/GROOVY/Embedding+Groovy)
(hopefully I used it in a good way ;) )  because:
> The most complete solution for people who want to embed groovy scripts into their servers and have them reloaded on modification is the GroovyScriptEngine. You initialize the GroovyScriptEngine with a set of CLASSPATH like roots that can be URLs or directory names. You can then execute any Groovy script within those roots. The GSE will also track dependencies between scripts so that if any dependent script is modified the whole tree will be recompiled and reloaded.
I wanted to have some way of caching the compiled classes in order not to have any issues with PermGen.
Anyway you can see that I am doing some conversions to have the URL of the classpath Groovy script resource. At the end we are extracting a class from the Groovy script and we are casting it to the Transformer.
**AbstractGroovyXmlTransformer.groovy**
```java
package groovyimport com.blogspot.toomuchcoding.transformer.Transformerimport groovy.util.slurpersupport.NodeChildrenimport groovy.xml.MarkupBuilder/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 23.03.13 * Time: 02:16 */abstract class AbstractGroovyXmlTransformer implements Transformer<String, String> {
    static Map<String, Object> MISSING_PROPERTIES = ["convertDate": new DateConverter(), "map": new Mapper()]    @Override    String transform(String input) {
        def inputXml = new XmlSlurper().parseText input        def writer = new StringWriter()        def outputXml = new MarkupBuilder(writer)        doTransform inputXml, outputXml        writer.toString()
}
    abstract void doTransform(inputXml, outputXml)    def propertyMissing(String name) {
        Object property = MISSING_PROPERTIES[name]        assert property != null, "There is no function like [$name]. The ones that are supported are ${
MISSING_PROPERTIES.keySet()
}
"        property
}
    protected static class Mapper {
        private Map<String, String> inputParameters        Mapper given(Map inputParameters) {
            this.inputParameters = inputParameters            this
}
        String from(NodeChildren nodeChildren) {
            assert inputParameters != null, "The mapping can't be null!"            assert nodeChildren != null, "Node can't be null!"            String nodeText = nodeChildren.text()            String mappedValue = inputParameters[nodeText]            mappedValue ?: inputParameters.default
}
        static Mapper map(Map<String, String> inputParameters) {
            return new Mapper(inputParameters)
}

}
    protected static class DateConverter {
        private String inputDate        private String inputDateFormat        DateConverter from(NodeChildren nodeChildren) {
            this.inputDate = nodeChildren.text()            this
}
        DateConverter havingDateFormat(String inputDateFormat) {
            this.inputDateFormat = inputDateFormat            this
}
        String toOutputDateFormat(String outputDateFormat) {
            assert inputDate != null, "The input date for which you are trying to do the conversion can't be null"            assert inputDateFormat != null, "The input date format for which you are trying to do the conversion can't be null"            assert outputDateFormat != null, "The output date format for which you are trying to do the conversion can't be null"            Date.parse(inputDateFormat, inputDate).format(outputDateFormat)
}
        static DateConverter convertDate() {
            new DateConverter()
}

}

}
```
```

```
In this abstract Groovy class I decided to place all the logic that could blur the image for the BA. In addition to that I created some helper classes and methods. In order to fully use the Groovy's DSL capabilities I used the propertyMissing method to map the words
**"map"**
and
**"convertDate"**
to create the instances of the helper classes which are used in the
**Builder**
design pattern way:
```
convertDate.from(inputXml.InputSystemContext.InputDate).havingDateFormat("dd/MM/yyyy").toOutputDateFormat("yy/MM/dd")ormap.given("Some_action" : "Some_output_action", "default" : "something else").from(inputXml.AdditionalData.TypeOfAction)
```
If there is no such "function" (for example a BA makes a typo or sth) then an assertion error is being thrown and a list of supported "function" (which in reality are properties - but they are functions from the BA's perspective) is being printed.
Now let's move to the script that would be used by the BA.
**GroovyXmlTransformer.groovy**
```java
package groovy/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 22.03.13 * Time: 23:59 * * additional functions: * * convertDate.from(Node).havingDateFormat("DateFormat").toOutputDateFormat("AnotherDateFormat") * map.given("Value to be mapped from" : "Value to be mapped to", "default" : "default value").from(Node) * */class GroovyXmlTransformer extends AbstractGroovyXmlTransformer {
    @Override    void doTransform(inputXml, outputXml) {
        outputXml.OutputSystemEnvelope() {
            OutputSystemContext {
                ResponseID(inputXml.InputSystemContext.RequestID.text().reverse())                OutputSource('OUTPUT_SYSTEM')                OutputDate(convertDate.from(inputXml.InputSystemContext.InputDate).havingDateFormat("dd/MM/yyyy").toOutputDateFormat("yy/MM/dd"))
}
            OutputAdditionalData {
                OutputReferenceNo("SOME_PREFIX_${
inputXml.AdditionalData.ReferenceNo.text()
}
_SOME_SUFIX")                OutputTypeOfAction(map.given("Some_action" : "Some_output_action", "default" : "something else").from(inputXml.AdditionalData.TypeOfAction))                OutputTransactions {
                    inputXml.AdditionalData.Transactions.Transaction.each {
                        OutputTransaction(Client: it.Client, ProductType: it.ProductType, 'Done')
}

}
                OutputProducts {
                    def minProduct = inputXml.AdditionalData.Products.Product.list().min {
 it.Value.text()
}
                    def maxProduct = inputXml.AdditionalData.Products.Product.list().max {
 it.Value.text()
}
                    MinProduct(name: minProduct.Name.text(), minProduct.Value.text())                    MaxProduct(name: maxProduct.Name.text(), maxProduct.Value.text())
}

}

}

}

}
```
This piece of code does the following mapping (You can check the
**/xml/SampleXml.xml**
):
Mapped from
Mapped to
InputSystemEnvelope
OutputSystemEnvelope
InputSystemContex
OutputSystemContex
RequestId
ResponseId (the Id should be reverted)
InputSource
OutputSoutce (constant "UTPUT_SYSTEM")
InputDate
OutputDate (converted from dd/MM/yyyy to yy/MM/dd)
InputAdditionalData
OutputAdditionalData
InputReferenceNo
OutputReferenceNo ( "SOME_PREFIX_" + value from InputReferenceNo + "_SOME_SUFIX")
InputTypeOfAction
OutputTypeOfAction (mapped in such a way that if InputTypeOfAction is equal to "Some_action" then we will have "Some_output_action". Otherwise we get "something else")
Transactions
OutputTransactions
Transaction
OutputTransaction ( Attribute Client from Transaction.Client, Attribute ProductType from Transaction.ProductType, and the value "Done")
Products
OutputProducts
Product having min value
MinProduct
Product having max value
MaxProduct
## The output
```
Converted from [<InputSystemEnvelope>    <InputSystemContext>        <RequestID>1234567890</RequestID>        <InputSource>INPUT_SYSTEM</InputSource>        <InputDate>22/03/2013</InputDate>    </InputSystemContext>    <AdditionalData>        <ReferenceNo>Ref1234567</ReferenceNo>        <TypeOfAction>Some_action</TypeOfAction>        <Transactions>            <Transaction>                <Client>ACME</Client>                <ProductType>IRS</ProductType>            </Transaction>            <Transaction>                <Client>Oracle</Client>                <ProductType>DB</ProductType>            </Transaction>        </Transactions>        <Products>            <Product>                <Name>Book</Name>                <Value>1</Value>            </Product>            <Product>                <Name>Car</Name>                <Value>10000</Value>            </Product>            <Product>                <Name>Boat</Name>                <Value>100000000</Value>            </Product>            <Product>                <Name>Spaceship</Name>                <Value>1000000000000000000</Value>            </Product>        </Products>    </AdditionalData></InputSystemEnvelope>] to[<OutputSystemEnvelope>  <OutputSystemContext>    <ResponseID>0987654321</ResponseID>    <OutputSource>OUTPUT_SYSTEM</OutputSource>    <OutputDate>13/03/22</OutputDate>  </OutputSystemContext>  <OutputAdditionalData>    <OutputReferenceNo>SOME_PREFIX_Ref1234567_SOME_SUFIX</OutputReferenceNo>    <OutputTypeOfAction>Some_output_action</OutputTypeOfAction>    <OutputTransactions>      <OutputTransaction Client='ACME' ProductType='IRS'>Done</OutputTransaction>      <OutputTransaction Client='Oracle' ProductType='DB'>Done</OutputTransaction>    </OutputTransactions>    <OutputProducts>      <MinProduct name='Book'>1</MinProduct>      <MaxProduct name='Spaceship'>1000000000000000000</MaxProduct>    </OutputProducts>  </OutputAdditionalData></OutputSystemEnvelope>]
```
## Pros and cons
The pros and cons of this approach are as follows:
Pros:
- The mapping is done sequentialy - field by field (it is easier to debug the problem)
- The mapping consists of vocabulary understandable by the BA
- Most of mappings could be done by the BA
- The majority of non-mapping grammar is hidden in the abstraction
- The compilation of the Groovy script is faster than creation of KnowledgeBases and compilation of Drools scripts
- Independence on the XML schema (each change of the schema would require the recompilation of the JAXB classes)
Cons:
- The BA would have to have some knowledge from the domain of computer science
- No parallel mapping
- The mapping might get less readable due to the fact that it is highly probable that the BA (out of lack of time) won't create a single function - all the logic will end up in the closures for a given Node.
- There might be some memory issues with parsing and recompilation of the Groovy scripts
- No XML schema may lead to improper output / input XML path setting
## Summary
The problem with mapping that we encountered in our project turned out to be a very interesting issue to deal with. The example shown in this post is only a proposition of solving the issue and hopefully could be a starting point to a further discussion on the topic. If you have any ideas or opinions on this topic please leave a comment under
[this article](https://toomuchcoding.com/2013/03/execution-of-groovy-scripts-from-java.html)
.
The sources can be found on the
[Too Much Coding BitBucket repository](https://bitbucket.org/gregorin1987/too-much-coding/src/69f4b59e4452e630670c71150d125e7ea86170aa/Groovy/Mappings?at=default)
and on
[GitHub](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Groovy/Mappings)
.
