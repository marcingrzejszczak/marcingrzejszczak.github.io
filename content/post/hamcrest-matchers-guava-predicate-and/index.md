---
title: "Hamcrest Matchers Guava Predicate And"
summary: "Hi coding addicts :) Often, while coding we have to deal with some POJO objects that have dozens of fields in them."
date: 2013-01-03

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Blog

content_meta:
  trending: false
---
Hi coding addicts :)
Often, while coding we have to deal with some POJO objects that have dozens of fields in them. Many times we initialize those classes through a constructor having dozens of arguments which is terrible in any possibly imaginable way :) Apart from that the functions that use those constructors are hardly testable. Let's take a closer look at using a Builder to change that situation, together with Hamcrest matchers and Guava Predicates to unit test it.
Let's start off with taking a look at the POJO class.
[]()
**SomeBigPojo.java**
```java
package pl.grzejszczak.marcin.junit.matchers.pojo;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 21:05 */public class SomeBigPojo {
    private String stringField0;
private Integer integerField0;
private Boolean booleanField0;
private String stringField1;
private Integer integerField1;
private Boolean booleanField1;
private String stringField2;
private Integer integerField2;
private Boolean booleanField2;
private String stringField3;
private Integer integerField3;
private Boolean booleanField3;
private String stringField4;
private Integer integerField4;
private Boolean booleanField4;
private String stringField5;
private Integer integerField5;
private Boolean booleanField5;
private String stringField6;
private Integer integerField6;
private Boolean booleanField6;
private String stringField7;
private String stringField8;
private String stringField9;
public SomeBigPojo(String stringField0, Integer integerField0, Boolean booleanField0, String stringField1, Integer integerField1, Boolean booleanField1, String stringField2, Integer integerField2, Boolean booleanField2, String stringField3, Integer integerField3, Boolean booleanField3, String stringField4, Integer integerField4, Boolean booleanField4, String stringField5, Integer integerField5, Boolean booleanField5, String stringField6, Integer integerField6, Boolean booleanField6, String stringField7, String stringField8, String stringField9) {
        this.stringField0 = stringField0;
this.integerField0 = integerField0;
this.booleanField0 = booleanField0;
this.stringField1 = stringField1;
this.integerField1 = integerField1;
this.booleanField1 = booleanField1;
this.stringField2 = stringField2;
this.integerField2 = integerField2;
this.booleanField2 = booleanField2;
this.stringField3 = stringField3;
this.integerField3 = integerField3;
this.booleanField3 = booleanField3;
this.stringField4 = stringField4;
this.integerField4 = integerField4;
this.booleanField4 = booleanField4;
this.stringField5 = stringField5;
this.integerField5 = integerField5;
this.booleanField5 = booleanField5;
this.stringField6 = stringField6;
this.integerField6 = integerField6;
this.booleanField6 = booleanField6;
this.stringField7 = stringField7;
this.stringField8 = stringField8;
this.stringField9 = stringField9;
}
    public String getStringField0() {
        return stringField0;
}
    public void setStringField0(String stringField0) {
        this.stringField0 = stringField0;
}
    public Integer getIntegerField0() {
        return integerField0;
}
    public void setIntegerField0(Integer integerField0) {
        this.integerField0 = integerField0;
}
    public Boolean getBooleanField0() {
        return booleanField0;
}
    public void setBooleanField0(Boolean booleanField0) {
        this.booleanField0 = booleanField0;
}
    public String getStringField1() {
        return stringField1;
}
    public void setStringField1(String stringField1) {
        this.stringField1 = stringField1;
}
    public Integer getIntegerField1() {
        return integerField1;
}
    public void setIntegerField1(Integer integerField1) {
        this.integerField1 = integerField1;
}
    public Boolean getBooleanField1() {
        return booleanField1;
}
    public void setBooleanField1(Boolean booleanField1) {
        this.booleanField1 = booleanField1;
}
    public String getStringField2() {
        return stringField2;
}
    public void setStringField2(String stringField2) {
        this.stringField2 = stringField2;
}
    public Integer getIntegerField2() {
        return integerField2;
}
    public void setIntegerField2(Integer integerField2) {
        this.integerField2 = integerField2;
}
    public Boolean getBooleanField2() {
        return booleanField2;
}
    public void setBooleanField2(Boolean booleanField2) {
        this.booleanField2 = booleanField2;
}
    public String getStringField3() {
        return stringField3;
}
    public void setStringField3(String stringField3) {
        this.stringField3 = stringField3;
}
    public Integer getIntegerField3() {
        return integerField3;
}
    public void setIntegerField3(Integer integerField3) {
        this.integerField3 = integerField3;
}
    public Boolean getBooleanField3() {
        return booleanField3;
}
    public void setBooleanField3(Boolean booleanField3) {
        this.booleanField3 = booleanField3;
}
    public String getStringField4() {
        return stringField4;
}
    public void setStringField4(String stringField4) {
        this.stringField4 = stringField4;
}
    public Integer getIntegerField4() {
        return integerField4;
}
    public void setIntegerField4(Integer integerField4) {
        this.integerField4 = integerField4;
}
    public Boolean getBooleanField4() {
        return booleanField4;
}
    public void setBooleanField4(Boolean booleanField4) {
        this.booleanField4 = booleanField4;
}
    public String getStringField5() {
        return stringField5;
}
    public void setStringField5(String stringField5) {
        this.stringField5 = stringField5;
}
    public Integer getIntegerField5() {
        return integerField5;
}
    public void setIntegerField5(Integer integerField5) {
        this.integerField5 = integerField5;
}
    public Boolean getBooleanField5() {
        return booleanField5;
}
    public void setBooleanField5(Boolean booleanField5) {
        this.booleanField5 = booleanField5;
}
    public String getStringField6() {
        return stringField6;
}
    public void setStringField6(String stringField6) {
        this.stringField6 = stringField6;
}
    public Integer getIntegerField6() {
        return integerField6;
}
    public void setIntegerField6(Integer integerField6) {
        this.integerField6 = integerField6;
}
    public Boolean getBooleanField6() {
        return booleanField6;
}
    public void setBooleanField6(Boolean booleanField6) {
        this.booleanField6 = booleanField6;
}
    public String getStringField7() {
        return stringField7;
}
    public void setStringField7(String stringField7) {
        this.stringField7 = stringField7;
}
    public String getStringField8() {
        return stringField8;
}
    public void setStringField8(String stringField8) {
        this.stringField8 = stringField8;
}
    public String getStringField9() {
        return stringField9;
}
    public void setStringField9(String stringField9) {
        this.stringField9 = stringField9;
}
    @Override    public String toString() {
        final StringBuilder sb = new StringBuilder();
sb.append("SomeBigPojo");
sb.append("{
stringField0='").append(stringField0).append('\'');
sb.append(", integerField0=").append(integerField0);
sb.append(", booleanField0=").append(booleanField0);
sb.append(", stringField1='").append(stringField1).append('\'');
sb.append(", integerField1=").append(integerField1);
sb.append(", booleanField1=").append(booleanField1);
sb.append(", stringField2='").append(stringField2).append('\'');
sb.append(", integerField2=").append(integerField2);
sb.append(", booleanField2=").append(booleanField2);
sb.append(", stringField3='").append(stringField3).append('\'');
sb.append(", integerField3=").append(integerField3);
sb.append(", booleanField3=").append(booleanField3);
sb.append(", stringField4='").append(stringField4).append('\'');
sb.append(", integerField4=").append(integerField4);
sb.append(", booleanField4=").append(booleanField4);
sb.append(", stringField5='").append(stringField5).append('\'');
sb.append(", integerField5=").append(integerField5);
sb.append(", booleanField5=").append(booleanField5);
sb.append(", stringField6='").append(stringField6).append('\'');
sb.append(", integerField6=").append(integerField6);
sb.append(", booleanField6=").append(booleanField6);
sb.append(", stringField7='").append(stringField7).append('\'');
sb.append(", stringField8='").append(stringField8).append('\'');
sb.append(", stringField9='").append(stringField9).append('\'');
sb.append('
}
');
return sb.toString();
}

}
```
Now take a look at the builder class that was used in order to get rid of usage of the humongous constructor. What is more you can set whatever you want to without the need to enter nulls for the undesired fields.
**SomeBigPojoBuilder.java**
```java
package pl.grzejszczak.marcin.junit.matchers.builder;
import pl.grzejszczak.marcin.junit.matchers.pojo.SomeBigPojo;
import static com.google.common.base.Preconditions.checkNotNull;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 21:08 */public class SomeBigPojoBuilder {
    /** A field with some default value */    private String stringField0 = "defaultValueForString0";
/** A field with some default value */    private Integer integerField0 = 100;
/** A field with some default value */    private Boolean booleanField0 = true;
private String stringField1;
private Integer integerField1;
private Boolean booleanField1;
private String stringField2;
private Integer integerField2;
private Boolean booleanField2;
private String stringField3;
private Integer integerField3;
private Boolean booleanField3;
private String stringField4;
private Integer integerField4;
private Boolean booleanField4;
private String stringField5;
private Integer integerField5;
private Boolean booleanField5;
private String stringField6;
private Integer integerField6;
private Boolean booleanField6;
private String stringField7;
private String stringField8;
private String stringField9;
public SomeBigPojoBuilder setStringField0(String stringField0) {
        this.stringField0 = stringField0;
return this;
}
    public SomeBigPojoBuilder setIntegerField0(Integer integerField0) {
        this.integerField0 = integerField0;
return this;
}
    public SomeBigPojoBuilder setBooleanField0(Boolean booleanField0) {
        this.booleanField0 = booleanField0;
return this;
}
    public SomeBigPojoBuilder setStringField1(String stringField1) {
        this.stringField1 = stringField1;
return this;
}
    public SomeBigPojoBuilder setIntegerField1(Integer integerField1) {
        this.integerField1 = integerField1;
return this;
}
    public SomeBigPojoBuilder setBooleanField1(Boolean booleanField1) {
        this.booleanField1 = booleanField1;
return this;
}
    public SomeBigPojoBuilder setStringField2(String stringField2) {
        this.stringField2 = stringField2;
return this;
}
    public SomeBigPojoBuilder setIntegerField2(Integer integerField2) {
        this.integerField2 = integerField2;
return this;
}
    public SomeBigPojoBuilder setBooleanField2(Boolean booleanField2) {
        this.booleanField2 = booleanField2;
return this;
}
    public SomeBigPojoBuilder setStringField3(String stringField3) {
        this.stringField3 = stringField3;
return this;
}
    public SomeBigPojoBuilder setIntegerField3(Integer integerField3) {
        this.integerField3 = integerField3;
return this;
}
    public SomeBigPojoBuilder setBooleanField3(Boolean booleanField3) {
        this.booleanField3 = booleanField3;
return this;
}
    public SomeBigPojoBuilder setStringField4(String stringField4) {
        this.stringField4 = stringField4;
return this;
}
    public SomeBigPojoBuilder setIntegerField4(Integer integerField4) {
        this.integerField4 = integerField4;
return this;
}
    public SomeBigPojoBuilder setBooleanField4(Boolean booleanField4) {
        this.booleanField4 = booleanField4;
return this;
}
    public SomeBigPojoBuilder setStringField5(String stringField5) {
        this.stringField5 = stringField5;
return this;
}
    public SomeBigPojoBuilder setIntegerField5(Integer integerField5) {
        this.integerField5 = integerField5;
return this;
}
    public SomeBigPojoBuilder setBooleanField5(Boolean booleanField5) {
        this.booleanField5 = booleanField5;
return this;
}
    public SomeBigPojoBuilder setStringField6(String stringField6) {
        this.stringField6 = stringField6;
return this;
}
    public SomeBigPojoBuilder setIntegerField6(Integer integerField6) {
        this.integerField6 = integerField6;
return this;
}
    public SomeBigPojoBuilder setBooleanField6(Boolean booleanField6) {
        this.booleanField6 = booleanField6;
return this;
}
    public SomeBigPojoBuilder setStringField7(String stringField7) {
        this.stringField7 = stringField7;
return this;
}
    public SomeBigPojoBuilder setStringField8(String stringField8) {
        this.stringField8 = stringField8;
return this;
}
    public SomeBigPojoBuilder setStringField9(String stringField9) {
        this.stringField9 = stringField9;
return this;
}
    /**     * Some function checking the state of our POJO     */    private void checkState() {
        checkNotNull(stringField1, "StringField1 must not be null!");
}
    public SomeBigPojo createSomeBigPojoWithBuilder() {
        checkState();
return new SomeBigPojo(stringField0, integerField0, booleanField0, stringField1, integerField1, booleanField1, stringField2, integerField2, booleanField2, stringField3, integerField3, booleanField3, stringField4, integerField4, booleanField4, stringField5, integerField5, booleanField5, stringField6, integerField6, booleanField6, stringField7, stringField8, stringField9);
}

}
```
Take a look at the function checkState that verifies whether the object is of a desired state. In this way we may disallow the creation of an object if some conditions where not fullfiled. For that case we are using the Guava's Predicate - checkNotNull method.
Note that the concept behind the Builder pattern is to delegate creation of an object to a Builder. In fact in the pure Builder design pattern implementation we would have to have a Director, some abstraction over a Builder and the concrete implementation of the Builder as such. In our case we have a simplification of that pattern - I used the refactoring option of IntelliJ. What can be done to make it look even better is to remove the constructor from the POJO and pass the values only by setters (that's what in fact I did in one of my projects ;) )
Now let's take a look at some usage examples:
****
**SomeServiceImpl.java **
```java
package pl.grzejszczak.marcin.junit.matchers.service;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import pl.grzejszczak.marcin.junit.matchers.builder.SomeBigPojoBuilder;
import pl.grzejszczak.marcin.junit.matchers.pojo.SomeBigPojo;
import static java.lang.String.format;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 21:25 */public class SomeServiceImpl implements SomeService {
    private static final Logger LOGGER = LoggerFactory.getLogger(SomeServiceImpl.class);
/** Could be an abstraction of builders injected by a setter - possible to mock */    private SomeBigPojoBuilder someBigPojoBuilder;
/**     * Hard to unit test     */    @Override    public void someLogicForAPojoWithoutBuilder() {
        LOGGER.debug("SomeLogicForAPojoWithoutBuilder executed");
SomeBigPojo someBigPojo = new SomeBigPojo("string", 1, false, "other string", 123, true, "something else", 321, false, "yet another string", 111, true, "something", 2, false, "More", 3, true, "String", 12, false, "some", "value", "ofString");
// Any chance of knowing what is the value of stringField8 basing on the constructor?        LOGGER.debug(format("StringField8 is equal [%s]%n", someBigPojo.getStringField8()));
// Print the object        LOGGER.debug(someBigPojo.toString());
}
    @Override    public void someLogicForAPojoWithBuilder() {
        LOGGER.debug("SomeLogicForAPojoWithBuilder executed");
SomeBigPojo someBigPojo = someBigPojoBuilder                .setStringField0("string")                .setIntegerField0(1)                .setBooleanField0(false)                .setStringField1("other string")                .setIntegerField1(123)                .setBooleanField1(true)                .setStringField2("something else")                .setIntegerField2(321)                .setBooleanField2(false)                .setStringField3("yet another string")                .setIntegerField3(111)                .setBooleanField3(false)                .setStringField4("something")                .setIntegerField4(2)                .setBooleanField4(false)                .setStringField5("More")                .setIntegerField5(3)                .setBooleanField5(true)                .setStringField6("String")                .setIntegerField6(12)                .setBooleanField6(false)                .setStringField7("some")                .setStringField8("value")                .setStringField9("ofString")                .createSomeBigPojoWithBuilder();
// Looking at the builder now I guess it's obvious what the value of StringField8        LOGGER.debug(format("StringField8 is equal [%s]%n", someBigPojo.getStringField8()));
// Print the object        LOGGER.debug(someBigPojo.toString());
}
    @Override    public void someLogicForAPojoWithBuilderBadArgument() {
        LOGGER.debug("someLogicForAPojoWithBuilderBadArgument executed");
SomeBigPojo someBigPojo = someBigPojoBuilder                .setStringField0("string")                .setIntegerField0(1)                .setBooleanField0(true)                .setIntegerField1(123)                .setBooleanField1(true)                .setStringField2("something else")                .setIntegerField2(321)                .setBooleanField2(false)                .setStringField3("yet another string")                .setIntegerField3(111).setBooleanField3(false)                .setStringField4("something")                .setIntegerField4(2)                .setBooleanField4(false)                .setStringField5("More")                .setIntegerField5(3)                .setBooleanField5(true)                .setStringField6("String")                .setIntegerField6(12)                .setBooleanField6(false)                .setStringField7("some")                .setStringField8("value")                .setStringField9("ofString")                .createSomeBigPojoWithBuilder();
// Print the object - will we even see an output        LOGGER.debug(someBigPojo.toString());
}
    public void setSomeBigPojoBuilder(SomeBigPojoBuilder someBigPojoBuilder) {
        this.someBigPojoBuilder = someBigPojoBuilder;
}

}
```
Notice how clear it is now to create an object and how easy is to define if a field has been set or not.
Let's move on to Hamcrest matchers that will help us in unit testing of our classes. I will not try to do the complete, 100% code coverage - the idea behind this post is to show how Hamcrest Matchers can become an addition to your unit tests.
Often unit tests are quite unclear and look like this:
****
**SomeBigPojoBuilderNoMatchersAndNoRefactoringTest.java **
****
```

```
```java
package pl.grzejszczak.marcin.junit.matchers.builder;
import org.junit.Before;
import org.junit.Test;
import pl.grzejszczak.marcin.junit.matchers.pojo.SomeBigPojo;
import static junit.framework.Assert.assertTrue;
import static org.apache.commons.lang.StringUtils.isNumeric;
import static pl.grzejszczak.marcin.junit.matchers.pojo.SomePojoConstants.*;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 23:02 */public class SomeBigPojoBuilderNoMatchersAndNoRefactoringTest {
    private SomeBigPojoBuilder objectUnderTest;
@Before    public void setUp() {
        objectUnderTest = new SomeBigPojoBuilder();
}
    @Test    public void testCreateSomeBigPojoWithBuilder() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setBooleanField1(true)                .setStringField0("1")                .setStringField1("12")                .setStringField2("123")                .setStringField3("1234")                .setStringField4("12345")                .setStringField5("123456")                .setStringField6("1234567")                .setStringField7("12345678")                .setStringField8("123456789")                .setStringField9("1234567890")                .createSomeBigPojoWithBuilder();
isPojoProperlyBuilt(someBigPojo);
}
    @Test(expected = AssertionError.class)    public void testCreateSomeBigPojoWithBuilderWrongFields() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setStringField0("0")                .setStringField1("Too long")                .createSomeBigPojoWithBuilder();
isPojoProperlyBuilt(someBigPojo);
}
    private void isPojoProperlyBuilt(SomeBigPojo someBigPojo) {
        assertTrue(someBigPojo.getStringField0().length() == STRING_FIELD_0_LENGTH);
assertTrue(isNumeric(someBigPojo.getStringField0()));
assertTrue(someBigPojo.getStringField1().length() == STRING_FIELD_1_LENGTH);
assertTrue(isNumeric(someBigPojo.getStringField0()));
assertTrue(someBigPojo.getStringField2().length() == STRING_FIELD_2_LENGTH);
assertTrue(isNumeric(someBigPojo.getStringField0()));
assertTrue(someBigPojo.getStringField3().length() == STRING_FIELD_3_LENGTH);
assertTrue(isNumeric(someBigPojo.getStringField0()));
assertTrue(someBigPojo.getStringField4().length() == STRING_FIELD_4_LENGTH);
assertTrue(isNumeric(someBigPojo.getStringField0()));
assertTrue(someBigPojo.getStringField5().length() == STRING_FIELD_5_LENGTH);
assertTrue(someBigPojo.getStringField6().length() == STRING_FIELD_6_LENGTH);
assertTrue(someBigPojo.getStringField7().length() == STRING_FIELD_7_LENGTH);
assertTrue(someBigPojo.getStringField8().length() == STRING_FIELD_8_LENGTH);
assertTrue(someBigPojo.getStringField9().length() == STRING_FIELD_9_LENGTH);
}

}
```
Simple refactoring can make them look nicer...
**SomeBigPojoBuilderNoMatchersTest.java **
****
```java
package pl.grzejszczak.marcin.junit.matchers.builder;
import org.junit.Before;
import org.junit.Test;
import pl.grzejszczak.marcin.junit.matchers.pojo.SomeBigPojo;
import static junit.framework.Assert.assertNotNull;
import static junit.framework.Assert.assertTrue;
import static org.apache.commons.lang.StringUtils.isNumeric;
import static pl.grzejszczak.marcin.junit.matchers.pojo.SomePojoConstants.*;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 23:02 */public class SomeBigPojoBuilderNoMatchersTest {
    private SomeBigPojoBuilder objectUnderTest;
@Before    public void setUp() {
        objectUnderTest = new SomeBigPojoBuilder();
}
    @Test    public void testCreateSomeBigPojoWithBuilder() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setBooleanField1(true)                .setStringField0("1")                .setStringField1("12")                .setStringField2("123")                .setStringField3("1234")                .setStringField4("12345")                .setStringField5("123456")                .setStringField6("1234567")                .setStringField7("12345678")                .setStringField8("123456789")                .setStringField9("1234567890")                .createSomeBigPojoWithBuilder();
isPojoProperlyBuilt(someBigPojo);
}
    @Test(expected = AssertionError.class)    public void testCreateSomeBigPojoWithBuilderWrongFields() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setStringField0("0")                .setStringField1("too long")                .createSomeBigPojoWithBuilder();
isPojoProperlyBuilt(someBigPojo);
}
    private void isPojoProperlyBuilt(SomeBigPojo someBigPojo) {
        isOfGivenLength(someBigPojo.getStringField0(), STRING_FIELD_0_LENGTH);
isFieldOfNumericValue(someBigPojo.getStringField0());
isOfGivenLength(someBigPojo.getStringField1(), STRING_FIELD_1_LENGTH);
isFieldOfNumericValue(someBigPojo.getStringField0());
isOfGivenLength(someBigPojo.getStringField2(), STRING_FIELD_2_LENGTH);
isFieldOfNumericValue(someBigPojo.getStringField0());
isOfGivenLength(someBigPojo.getStringField3(), STRING_FIELD_3_LENGTH);
isFieldOfNumericValue(someBigPojo.getStringField0());
isOfGivenLength(someBigPojo.getStringField4(), STRING_FIELD_4_LENGTH);
isFieldOfNumericValue(someBigPojo.getStringField0());
isOfGivenLength(someBigPojo.getStringField5(), STRING_FIELD_5_LENGTH);
isOfGivenLength(someBigPojo.getStringField6(), STRING_FIELD_6_LENGTH);
isOfGivenLength(someBigPojo.getStringField7(), STRING_FIELD_7_LENGTH);
isOfGivenLength(someBigPojo.getStringField8(), STRING_FIELD_8_LENGTH);
isOfGivenLength(someBigPojo.getStringField9(), STRING_FIELD_9_LENGTH);
}
    private void isOfGivenLength(String pojo, final Integer expectedLength) {
        assertNotNull(pojo);
assertTrue(expectedLength == pojo.length());
}
    private void isFieldOfNumericValue(String field) {
        assertTrue(isNumeric(field));
}

}
```
That looks nice, doesn't it? :) And how about using Matchers instead of functions?
**SomeBigPojoBuilderTest.kava **
```java
package pl.grzejszczak.marcin.junit.matchers.builder;
import org.hamcrest.BaseMatcher;
import org.hamcrest.Description;
import org.hamcrest.Matcher;
import org.junit.Before;
import org.junit.Test;
import pl.grzejszczak.marcin.junit.matchers.pojo.SomeBigPojo;
import static java.lang.String.format;
import static junit.framework.Assert.assertTrue;
import static org.apache.commons.lang.StringUtils.isNumeric;
import static org.junit.Assert.assertThat;
import static pl.grzejszczak.marcin.junit.matchers.pojo.SomePojoConstants.*;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 23:02 */public class SomeBigPojoBuilderTest {
    private SomeBigPojoBuilder objectUnderTest;
@Before    public void setUp() {
        objectUnderTest = new SomeBigPojoBuilder();
}
    @Test    public void testCreateSomeBigPojoWithBuilder() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setBooleanField1(true)                .setStringField0("1")                .setStringField1("12")                .setStringField2("123")                .setStringField3("1234")                .setStringField4("12345")                .setStringField5("123456")                .setStringField6("1234567")                .setStringField7("12345678")                .setStringField8("123456789")                .setStringField9("1234567890")                .createSomeBigPojoWithBuilder();
assertThat(someBigPojo, isPojoProperlyBuilt());
}
    @Test(expected = AssertionError.class)    public void testCreateSomeBigPojoWithBuilderWrongFields() throws Exception {
        SomeBigPojo someBigPojo = objectUnderTest                .setStringField0("0")                .setStringField1("Too long")                .createSomeBigPojoWithBuilder();
assertThat(someBigPojo, isPojoProperlyBuilt());
}
    /**     * Let us assume that there is a specific business case that we have to take into consideration regarding some particular field     *     * @return     */    private static Matcher isPojoProperlyBuilt() {
        return new BaseMatcher() {
            @Override            public boolean matches(Object o) {
                assertTrue(o instanceof SomeBigPojo);
SomeBigPojo someBigPojo = (SomeBigPojo) o;
assertThat(someBigPojo.getStringField0(), isOfGivenLength(STRING_FIELD_0_LENGTH));
assertThat(someBigPojo.getStringField0(), isFieldOfNumericValue());
assertThat(someBigPojo.getStringField1(), isOfGivenLength(STRING_FIELD_1_LENGTH));
assertThat(someBigPojo.getStringField1(), isFieldOfNumericValue());
assertThat(someBigPojo.getStringField2(), isOfGivenLength(STRING_FIELD_2_LENGTH));
assertThat(someBigPojo.getStringField2(), isFieldOfNumericValue());
assertThat(someBigPojo.getStringField3(), isOfGivenLength(STRING_FIELD_3_LENGTH));
assertThat(someBigPojo.getStringField3(), isFieldOfNumericValue());
assertThat(someBigPojo.getStringField4(), isOfGivenLength(STRING_FIELD_4_LENGTH));
assertThat(someBigPojo.getStringField4(), isFieldOfNumericValue());
assertThat(someBigPojo.getStringField5(), isOfGivenLength(STRING_FIELD_5_LENGTH));
assertThat(someBigPojo.getStringField6(), isOfGivenLength(STRING_FIELD_6_LENGTH));
assertThat(someBigPojo.getStringField7(), isOfGivenLength(STRING_FIELD_7_LENGTH));
assertThat(someBigPojo.getStringField8(), isOfGivenLength(STRING_FIELD_8_LENGTH));
assertThat(someBigPojo.getStringField9(), isOfGivenLength(STRING_FIELD_9_LENGTH));
return true;
}
            @Override            public void describeTo(Description description) {
                description.appendText("Lengths of fields are limited and the first 4 fields are numeric");
}

}
;
}
    private static Matcher isOfGivenLength(final Integer expectedLength) {
        return new BaseMatcher() {
            public boolean matches(Object o) {
                assertTrue(o instanceof String);
return expectedLength == String.valueOf(o).length();
}
            public void describeTo(Description description) {
                description.appendText(format("String's length should be equal to [%d]", expectedLength));
}

}
;
}
    private static Matcher isFieldOfNumericValue() {
        return new BaseMatcher() {
            public boolean matches(Object o) {
                assertTrue(o instanceof String);
return isNumeric(String.valueOf(o));
}
            public void describeTo(Description description) {
                description.appendText("The value of the field should be numeric");
}

}
;
}

}
```
The following main method executes the functions of the Service:
```java
package pl.grzejszczak.marcin.junit.matchers;
import pl.grzejszczak.marcin.junit.matchers.builder.SomeBigPojoBuilder;
import pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl;
/** * Created with IntelliJ IDEA. * User: mgrzejszczak * Date: 03.01.13 * Time: 22:38 */public class MatcherMain {
    public static void main(String[] args) {
        SomeServiceImpl someService = new SomeServiceImpl();
someService.setSomeBigPojoBuilder(new SomeBigPojoBuilder());
someService.someLogicForAPojoWithoutBuilder();
someService.setSomeBigPojoBuilder(new SomeBigPojoBuilder());
someService.someLogicForAPojoWithBuilder();
someService.setSomeBigPojoBuilder(new SomeBigPojoBuilder());
someService.someLogicForAPojoWithBuilderBadArgument();
}

}
```
And the logs are:
```groovy
pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 27 SomeLogicForAPojoWithoutBuilder executedpl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 30 StringField8 is equal [value]pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 32 SomeBigPojo{
  stringField0='string',
  integerField0=1,
  booleanField0=false,
  stringField1='other string',
  integerField1=123,
  booleanField1=true,
  stringField2='something else',
  integerField2=321,
  booleanField2=false,
  stringField3='yet another string',
  integerField3=111,
  booleanField3=true,
  stringField4='something',
  integerField4=2,
  booleanField4=false,
  stringField5='More',
  integerField5=3,
  booleanField5=true,
  stringField6='String',
  integerField6=12,
  booleanField6=false,
  stringField7='some',
  stringField8='value',
  stringField9='ofString'
}
pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 37 SomeLogicForAPojoWithBuilder executedpl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 65 StringField8 is equal [value]pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 67 SomeBigPojo{
  stringField0='string',
  integerField0=1,
  booleanField0=false,
  stringField1='other string',
  integerField1=123,
  booleanField1=true,
  stringField2='something else',
  integerField2=321,
  booleanField2=false,
  stringField3='yet another string',
  integerField3=111,
  booleanField3=false,
  stringField4='something',
  integerField4=2,
  booleanField4=false,
  stringField5='More',
  integerField5=3,
  booleanField5=true,
  stringField6='String',
  integerField6=12,
  booleanField6=false,
  stringField7='some',
  stringField8='value',
  stringField9='ofString'
}
pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl: 72 someLogicForAPojoWithBuilderBadArgument executedException in thread "main" java.lang.NullPointerException: StringField1 must not be null! at com.google.common.base.Preconditions.checkNotNull(Preconditions.java: 208) at pl.grzejszczak.marcin.junit.matchers.builder.SomeBigPojoBuilder.checkState(SomeBigPojoBuilder.java: 166) at pl.grzejszczak.marcin.junit.matchers.builder.SomeBigPojoBuilder.createSomeBigPojoWithBuilder(SomeBigPojoBuilder.java: 170) at pl.grzejszczak.marcin.junit.matchers.service.SomeServiceImpl.someLogicForAPojoWithBuilderBadArgument(SomeServiceImpl.java: 73) at pl.grzejszczak.marcin.junit.matchers.MatcherMain.main(MatcherMain.java: 23) at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java: 39) at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java: 25) at java.lang.reflect.Method.invoke(Method.java: 597) at com.intellij.rt.execution.application.AppMain.main(AppMain.java: 120)
```
In my opinion that looks really nice :) And what is yours?
[Sources are available here at Too Much Codings code repository.](https://bitbucket.org/gregorin1987/too-much-coding/src/36becc07728e/Unit%20Testing%20-%20Matchers?at=default)
## UPDATE
I've made some code changes and cleaning (not much though cause I didn't have too much time) and the code is available at github -
[https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit_Testing_-_Matchers](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit_Testing_-_Matchers)
