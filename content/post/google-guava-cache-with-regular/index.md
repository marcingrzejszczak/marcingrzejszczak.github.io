---
title: "Google Guava Cache With Regular"
summary: "Hi! Merry Christmas everyone :) Quite recently I've seen a nice presentation about Google Guava and we came to the conclusion in our project that it could be really interesting to use the its Cache functionallity ."
date: 2012-12-24

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
Hi!  Merry Christmas everyone :)  Quite recently I've seen
[a nice presentation about Google Guava](https://toomuchcoding.com/2012/12/google-guava.html)
and we came to the conclusion in our project that it could be really interesting to use the
[its Cache functionallity](https://code.google.com/p/guava-libraries/wiki/CachesExplained)
.  Let us take a look at the regexp Pattern class and its
[compile function](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#compile(java.lang.String))
. Quite often in the code we can see that each time a regular expression is being used a programmer is repeatidly calling the aforementioned Pattern.compile() function with the same argument thus compiling the same regular expression over and over again. What could be done however is to cache the result of such compilations - let us take a look at the RegexpUtils utility class:
**RegexpUtils.java **
****
```java
package pl.grzejszczak.marcin.guava.cache.utils;
import com.google.common.cache.CacheBuilder;
import com.google.common.cache.CacheLoader;
import com.google.common.cache.LoadingCache;
import java.util.concurrent.ExecutionException;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import static java.lang.String.format;
public final class RegexpUtils {
    private RegexpUtils() {
        throw new UnsupportedOperationException("RegexpUtils is a utility class - don't instantiate it!");
}
    private static final LoadingCache<String, Pattern> COMPILED_PATTERNS =            CacheBuilder.newBuilder().build(new CacheLoader<String, Pattern>() {
                @Override                public Pattern load(String regexp) throws Exception {
                    return Pattern.compile(regexp);
}

}
);
public static Pattern getPattern(String regexp) {
        try {
            return COMPILED_PATTERNS.get(regexp);
}
 catch (ExecutionException e) {
            throw new RuntimeException(format("Error when getting a pattern [%s] from cache", regexp), e);
}

}
    public static boolean matches(String stringToCheck, String regexp) {
        return doGetMatcher(stringToCheck, regexp).matches();
}
    public static Matcher getMatcher(String stringToCheck, String regexp) {
        return doGetMatcher(stringToCheck, regexp);
}
    private static Matcher doGetMatcher(String stringToCheck, String regexp) {
        Pattern pattern = getPattern(regexp);
return pattern.matcher(stringToCheck);
}

}
```
As you can see the Guava's LoadingCache with the CacheBuilder is being used to populate a cache with a new compiled pattern if one is not found. Due to caching the compiled pattern if a compilation has already taken place it will not be repeated ever again (in our case since we dno't have any expiry set).  Now a simple test
**GuavaCache.java **
****
```java
package pl.grzejszczak.marcin.guava.cache;
import com.google.common.base.Stopwatch;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import pl.grzejszczak.marcin.guava.cache.utils.RegexpUtils;
import java.util.regex.Pattern;
import static java.lang.String.format;
public class GuavaCache {
    private static final Logger LOGGER = LoggerFactory.getLogger(GuavaCache.class);
public static final String STRING_TO_MATCH = "something";
public static void main(String[] args) {
        runTestForManualCompilationAndOneUsingCache(1);
runTestForManualCompilationAndOneUsingCache(10);
runTestForManualCompilationAndOneUsingCache(100);
runTestForManualCompilationAndOneUsingCache(1000);
runTestForManualCompilationAndOneUsingCache(10000);
runTestForManualCompilationAndOneUsingCache(100000);
runTestForManualCompilationAndOneUsingCache(1000000);
}
    private static void runTestForManualCompilationAndOneUsingCache(int firstNoOfRepetitions) {
        repeatManualCompilation(firstNoOfRepetitions);
repeatCompilationWithCache(firstNoOfRepetitions);
}
    private static void repeatManualCompilation(int noOfRepetitions) {
        Stopwatch stopwatch = new Stopwatch().start();
compileAndMatchPatternManually(noOfRepetitions);
LOGGER.debug(format("Time needed to compile and check regexp expression [%d] ms, no of iterations [%d]", stopwatch.elapsedMillis(), noOfRepetitions));
}
    private static void repeatCompilationWithCache(int noOfRepetitions) {
        Stopwatch stopwatch = new Stopwatch().start();
compileAndMatchPatternUsingCache(noOfRepetitions);
LOGGER.debug(format("Time needed to compile and check regexp expression using Cache [%d] ms, no of iterations [%d]", stopwatch.elapsedMillis(), noOfRepetitions));
}
    private static void compileAndMatchPatternManually(int limit) {
        for (int i = 0;
i < limit;
i++) {
            Pattern.compile("something").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something1").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something2").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something3").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something4").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something5").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something6").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something7").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something8").matcher(STRING_TO_MATCH).matches();
Pattern.compile("something9").matcher(STRING_TO_MATCH).matches();
}

}
    private static void compileAndMatchPatternUsingCache(int limit) {
        for (int i = 0;
i < limit;
i++) {
            RegexpUtils.matches(STRING_TO_MATCH, "something");
RegexpUtils.matches(STRING_TO_MATCH, "something1");
RegexpUtils.matches(STRING_TO_MATCH, "something2");
RegexpUtils.matches(STRING_TO_MATCH, "something3");
RegexpUtils.matches(STRING_TO_MATCH, "something4");
RegexpUtils.matches(STRING_TO_MATCH, "something5");
RegexpUtils.matches(STRING_TO_MATCH, "something6");
RegexpUtils.matches(STRING_TO_MATCH, "something7");
RegexpUtils.matches(STRING_TO_MATCH, "something8");
RegexpUtils.matches(STRING_TO_MATCH, "something9");
}

}

}
```
We are running a series of tests and checking the time of their execution. Note that the results of these tests are not precise due to the fact that the application is not being run in isolation so numerous conditions can affect the time of the execution. We are interested in showing some degree of the problem rather than showing the precise execution time.  For a given number of iterations (1,10,100,1000,10000,100000,1000000) we are either compiling 10 regular expressions or using a Guava's cache to retrieve the compiled Pattern and then we match them against a string to match.  These are the logs:
```groovy
pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [1] ms,
no of iterations [1]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [35] ms,
no of iterations [1]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [1] ms,
no of iterations [10]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [0] ms,
no of iterations [10]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [8] ms,
no of iterations [100]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [3] ms,
no of iterations [100]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [10] ms,
no of iterations [1000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [10] ms,
no of iterations [1000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [83] ms,
no of iterations [10000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [33] ms,
no of iterations [10000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [800] ms,
no of iterations [100000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [279] ms,
no of iterations [100000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 34 Time needed to compile and check regexp expression [7562] ms,
no of iterations [1000000]pl.grzejszczak.marcin.guava.cache.GuavaCache: 40 Time needed to compile and check regexp expression using Cache [3067] ms,
no of iterations [1000000]
```
You can find the
[sources over here](https://bitbucket.org/gregorin1987/too-much-coding/src)
under the Guava/Cache directory or go to the url
[https://bitbucket.org/gregorin1987/too-much-coding/src](https://bitbucket.org/gregorin1987/too-much-coding/src)
