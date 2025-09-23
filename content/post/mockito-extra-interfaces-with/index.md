---
title: "Mockito Extra Interfaces With"
summary: "In the code I have quite recently came across a really bad piece of code that based on class casting in terms of performing some actions on objects."
date: 2013-06-12

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
In the code I have quite recently came across a really bad piece of code that based on class casting in terms of performing some actions on objects. Of course the code needed to be refactored but sometimes you can't do it / or don't want to do it (and it should be understandable) if first you don't have unit tests of that functionality. In the following post I will show how to test such code, how to refactor it and in fact what I think about such code ;)
[]()
Let's take a look at the project structure:
[![](https://3.bp.blogspot.com/-RpIw1Ccnof8/UbhBR8EHqbI/AAAAAAAABZU/cPRqLAPNCHU/s320/Mockito+Extra+Interfaces.jpg)](https://3.bp.blogspot.com/-RpIw1Ccnof8/UbhBR8EHqbI/AAAAAAAABZU/cPRqLAPNCHU/s1600/Mockito+Extra+Interfaces.jpg)
As presented in the post regarding
[Mocktio RETURNS_DEEP_STUBS Answer for JAXB](https://toomuchcoding.blogspot.com/2013/06/mockito-returndeepstubs-for-jaxb.html)
yet again we have the JAXB generated classes by the JAXB compiler in the
**com.blogspot.toomuchcoding.model**
package. Let's ommit the discussion over the
**pom.xml**
file since it's exactly the same as in the previous post.
In the
**com.blogspot.toomuchcoding.adapter**
package we have adapters over the JAXB PlayerDetails class that provides access to the Player interface. There is the
**CommonPlayerAdapter.java**
```java
package com.blogspot.toomuchcoding.adapter;
import com.blogspot.toomuchcoding.model.Player;
import com.blogspot.toomuchcoding.model.PlayerDetails;
/** * User: mgrzejszczak * Date: 09.06.13 * Time: 15:42 */public class CommonPlayerAdapter implements Player {
    private final PlayerDetails playerDetails;
public CommonPlayerAdapter(PlayerDetails playerDetails) {
        this.playerDetails = playerDetails;
}
    @Override    public void run() {
        System.out.printf("Run %s. Run!%n", playerDetails.getName());
}
    public PlayerDetails getPlayerDetails() {
        return playerDetails;
}

}
```
**DefencePlayerAdapter.java**
```java
package com.blogspot.toomuchcoding.adapter;
import com.blogspot.toomuchcoding.model.DJ;
import com.blogspot.toomuchcoding.model.DefensivePlayer;
import com.blogspot.toomuchcoding.model.JavaDeveloper;
import com.blogspot.toomuchcoding.model.PlayerDetails;
/** * User: mgrzejszczak * Date: 09.06.13 * Time: 15:42 */public class DefencePlayerAdapter extends CommonPlayerAdapter implements DefensivePlayer, DJ, JavaDeveloper {
    public DefencePlayerAdapter(PlayerDetails playerDetails) {
        super(playerDetails);
}
    @Override    public void defend() {
        System.out.printf("Defence! %s. Defence!%n", getPlayerDetails().getName());
}
    @Override    public void playSomeMusic() {
        System.out.println("Oops I did it again...!");
}
    @Override    public void doSomeSeriousCoding() {
        System.out.println("System.out.println(\"Hello world\");
");
}

}
```
**OffensivePlayerAdapter.java**
```java
package com.blogspot.toomuchcoding.adapter;
import com.blogspot.toomuchcoding.model.OffensivePlayer;
import com.blogspot.toomuchcoding.model.PlayerDetails;
/** * User: mgrzejszczak * Date: 09.06.13 * Time: 15:42 */public class OffensivePlayerAdapter extends CommonPlayerAdapter implements OffensivePlayer {
    public OffensivePlayerAdapter(PlayerDetails playerDetails) {
        super(playerDetails);
}
    @Override    public void shoot() {
        System.out.printf("%s Shooooot!.%n", getPlayerDetails().getName());
}

}
```
Ok, now let's go to the more interesting part. Let us assume that we have a very simple factory of players:
**PlayerFactoryImpl.java**
```java
package com.blogspot.toomuchcoding.factory;
import com.blogspot.toomuchcoding.adapter.CommonPlayerAdapter;
import com.blogspot.toomuchcoding.adapter.DefencePlayerAdapter;
import com.blogspot.toomuchcoding.adapter.OffensivePlayerAdapter;
import com.blogspot.toomuchcoding.model.Player;
import com.blogspot.toomuchcoding.model.PlayerDetails;
import com.blogspot.toomuchcoding.model.PositionType;
/** * User: mgrzejszczak * Date: 09.06.13 * Time: 15:53 */public class PlayerFactoryImpl implements PlayerFactory {
    @Override    public Player createPlayer(PositionType positionType) {
        PlayerDetails player = createCommonPlayer(positionType);
switch (positionType) {
            case ATT:                return new OffensivePlayerAdapter(player);
case MID:                return new OffensivePlayerAdapter(player);
case DEF:                return new DefencePlayerAdapter(player);
case GK:                return new DefencePlayerAdapter(player);
default:                return new CommonPlayerAdapter(player);
}

}
    private PlayerDetails createCommonPlayer(PositionType positionType) {
        PlayerDetails playerDetails = new PlayerDetails();
playerDetails.setPosition(positionType);
return playerDetails;
}

}
```
Ok so we have the factory that builds Players. Let's take a look at the Service that uses the factory:
**PlayerServiceImpl.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.factory.PlayerFactory;
import com.blogspot.toomuchcoding.model.*;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:02 */public class PlayerServiceImpl implements PlayerService {
    private PlayerFactory playerFactory;
@Override    public Player playAGameWithAPlayerOfPosition(PositionType positionType) {
        Player player = playerFactory.createPlayer(positionType);
player.run();
performAdditionalActions(player);
return player;
}
    private void performAdditionalActions(Player player) {
        if(player instanceof OffensivePlayer) {
            OffensivePlayer offensivePlayer = (OffensivePlayer) player;
performAdditionalActionsForTheOffensivePlayer(offensivePlayer);
}
else if(player instanceof DefensivePlayer) {
            DefensivePlayer defensivePlayer = (DefensivePlayer) player;
performAdditionalActionsForTheDefensivePlayer(defensivePlayer);
}

}
    private void performAdditionalActionsForTheOffensivePlayer(OffensivePlayer offensivePlayer) {
        offensivePlayer.shoot();
}
    private void performAdditionalActionsForTheDefensivePlayer(DefensivePlayer defensivePlayer) {
        defensivePlayer.defend();
try{
            DJ dj = (DJ)defensivePlayer;
dj.playSomeMusic();
JavaDeveloper javaDeveloper = (JavaDeveloper)defensivePlayer;
javaDeveloper.doSomeSeriousCoding();
}
catch(ClassCastException exception) {
            System.err.println("Sorry, I can't do more than just play football...");
}

}
    public PlayerFactory getPlayerFactory() {
        return playerFactory;
}
    public void setPlayerFactory(PlayerFactory playerFactory) {
        this.playerFactory = playerFactory;
}

}
```
Let's admit it... this code is bad. Internally when you look at it (regardless of the fact whether it used
**instance of**
operator or not) you feel that it is evil :) As you can see in the code we have some class casts going on... How on earth can we test it? In the majority of testing frameworks you can't do such class casts on mocks since they are built with the CGLIB library and there can be some ClassCastExceptions thrown. You could still not return mocks and real implementations (assuming that those will not perform any ugly stuff in the construction process) and it could actually work but still - this is bad code :P
Mockito comes to the rescue (although you shouldn't overuse this feature - in fact if you need to use it please consider refactoring it) with its
**extraInterfaces**
feature:
### extraInterfaces
> [MockSettings](https://mockito.googlecode.com/svn/branches/1.8.5/javadoc/org/mockito/MockSettings.html)
> **extraInterfaces**
> (java.lang.Class<?>... interfaces)
> Specifies extra interfaces the mock should implement. Might be useful for legacy code or some corner cases. For background, see issue 51
> [here](https://code.google.com/p/mockito/issues/detail?id=51)
> This mysterious feature should be used very occasionally. The object under test should know exactly its collaborators & dependencies. If you happen to use it often than please make sure you are really producing simple, clean & readable code.
> Examples:
> Foo foo = mock(Foo.class, withSettings().extraInterfaces(Bar.class, Baz.class));
> //now, the mock implements extra interfaces, so following casting is possible:
> Bar bar = (Bar) foo;
> Baz baz = (Baz) foo;
> **Parameters:**
> `interfaces`
> - extra interfaces the should implement.
> **Returns:**
> settings instance so that you can fluently specify other settings
Now let's take a look at the test:
**PlayerServiceImplTest.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.factory.PlayerFactory;
import com.blogspot.toomuchcoding.model.*;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.invocation.InvocationOnMock;
import org.mockito.runners.MockitoJUnitRunner;
import org.mockito.stubbing.Answer;
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;
import static org.mockito.BDDMockito.*;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:26 */@RunWith(MockitoJUnitRunner.class)public class PlayerServiceImplTest {
    @Mock    PlayerFactory playerFactory;
@InjectMocks    PlayerServiceImpl objectUnderTest;
@Mock(extraInterfaces = {
DJ.class, JavaDeveloper.class
}
)    DefensivePlayer defensivePlayerWithDjAndJavaDevSkills;
@Mock    DefensivePlayer defensivePlayer;
@Mock    OffensivePlayer offensivePlayer;
@Mock    Player commonPlayer;
@Test    public void shouldReturnOffensivePlayerThatRan() throws Exception {
        //given        given(playerFactory.createPlayer(PositionType.ATT)).willReturn(offensivePlayer);
//when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(PositionType.ATT);
//then        assertThat(createdPlayer == offensivePlayer, is(true));
verify(offensivePlayer).run();
}
    @Test    public void shouldReturnDefensivePlayerButHeWontBeADjNorAJavaDev() throws Exception {
        //given        given(playerFactory.createPlayer(PositionType.GK)).willReturn(defensivePlayer);
//when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(PositionType.GK);
//then        assertThat(createdPlayer == defensivePlayer, is(true));
verify(defensivePlayer).run();
verify(defensivePlayer).defend();
verifyNoMoreInteractions(defensivePlayer);
}
    @Test    public void shouldReturnDefensivePlayerBeingADjAndAJavaDev() throws Exception {
        //given        given(playerFactory.createPlayer(PositionType.GK)).willReturn(defensivePlayerWithDjAndJavaDevSkills);
doAnswer(new Answer<Object>() {
            @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
                System.out.println("Hit me baby one more time!");
return null;
}

}
).when(((DJ) defensivePlayerWithDjAndJavaDevSkills)).playSomeMusic();
doAnswer(new Answer<Object>() {
            @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
                System.out.println("public static void main(String... args) {
\n
}
");
return null;
}

}
).when(((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills)).doSomeSeriousCoding();
//when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(PositionType.GK);
//then        assertThat(createdPlayer == defensivePlayerWithDjAndJavaDevSkills, is(true));
verify(defensivePlayerWithDjAndJavaDevSkills).run();
verify(defensivePlayerWithDjAndJavaDevSkills).defend();
verify((DJ) defensivePlayerWithDjAndJavaDevSkills).playSomeMusic();
verify((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills).doSomeSeriousCoding();
}
    @Test    public void shouldReturnDefensivePlayerBeingADjAndAJavaDevByUsingWithSettings() throws Exception {
        //given        DefensivePlayer defensivePlayerWithDjAndJavaDevSkills = mock(DefensivePlayer.class, withSettings().extraInterfaces(DJ.class, JavaDeveloper.class));
given(playerFactory.createPlayer(PositionType.GK)).willReturn(defensivePlayerWithDjAndJavaDevSkills);
doAnswer(new Answer<Object>() {
            @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
                System.out.println("Hit me baby one more time!");
return null;
}

}
).when(((DJ) defensivePlayerWithDjAndJavaDevSkills)).playSomeMusic();
doAnswer(new Answer<Object>() {
            @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
                System.out.println("public static void main(String... args) {
\n
}
");
return null;
}

}
).when(((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills)).doSomeSeriousCoding();
//when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(PositionType.GK);
//then        assertThat(createdPlayer == defensivePlayerWithDjAndJavaDevSkills, is(true));
verify(defensivePlayerWithDjAndJavaDevSkills).run();
verify(defensivePlayerWithDjAndJavaDevSkills).defend();
verify((DJ) defensivePlayerWithDjAndJavaDevSkills).playSomeMusic();
verify((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills).doSomeSeriousCoding();
}
    @Test    public void shouldReturnCommonPlayer() throws Exception {
        //given        given(playerFactory.createPlayer(null)).willReturn(commonPlayer);
//when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(null);
//then        assertThat(createdPlayer, is(commonPlayer));
}

}
```
There are quite a few tests here so let's take a look at the most interesting ones.  But before we do it let's
We start with providing the
**@RunWith(MockitoJUnitRunner.class)**
annotation which alows us to use the Mockito annotations such as
**@Mock**
and
**@InjectMocks**
.
Speaking of which
**@Mock**
annotation creates a Mock whereas
**@InjectMocks**
inject all the mocks either by constructor or by setters (that's awesome isn't it? :) ).
For the defensive player we are using the extra element of the annotation
**extraInterfaces **
that provides additional interfaces for the given Mock. You can also write (what you can find in the
**shouldReturnDefensivePlayerBeingADjAndAJavaDevByUsingWithSettings**
test) :
```
DefensivePlayer defensivePlayerWithDjAndJavaDevSkills = mock(DefensivePlayer.class, withSettings().extraInterfaces(DJ.class, JavaDeveloper.class));
```
Let's take a closer look at the test that we wrote for the functionality related to the DefensivePlayer and the casting part of the tested function:
```groovy
@Test    public void shouldReturnDefensivePlayerBeingADjAndAJavaDev() throws Exception {
  //given        given (
  playerFactory.createPlayer(PositionType.GK)).willReturn(defensivePlayerWithDjAndJavaDevSkills);        doAnswer(new Answer<Object>() {
    @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
      System.out.println("Hit me baby one more time!");                return null;
    }
    
  }
  ).when (
  ((DJ) defensivePlayerWithDjAndJavaDevSkills)).playSomeMusic();        doAnswer(new Answer<Object>() {
    @Override            public Object answer(InvocationOnMock invocationOnMock) throws Throwable {
      System.out.println("public static void main(String... args){
        \n
      }
      ");                return null;
    }
    
  }
  ).when (
  ((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills)).doSomeSeriousCoding();        //when        Player createdPlayer = objectUnderTest.playAGameWithAPlayerOfPosition(PositionType.GK);        //then        assertThat(createdPlayer == defensivePlayerWithDjAndJavaDevSkills,
  is(true));        verify(defensivePlayerWithDjAndJavaDevSkills).run();        verify(defensivePlayerWithDjAndJavaDevSkills).defend();        verify((DJ) defensivePlayerWithDjAndJavaDevSkills).playSomeMusic();        verify((JavaDeveloper) defensivePlayerWithDjAndJavaDevSkills).doSomeSeriousCoding();
}
```
We are using the
**BDDMockito**
static methods like
**given(...).willReturn(...).willAnswer(...)**
etc. Then we are stubbing void methods with our custom Anwsers. In the next line you can see that in order to stub methods of another interface you have to cast the mock to the given interface. The same is related to the verification phase where i norder to check if a method was executed you have to cast the mock to the given interface.
You could improve the test by returning a real implementation from the factory or if it's a "heavy" operation to create one you could return a mock of such an implementation. What I wanted to show here is how to use the extra interfaces in Mockito (perhaps my usecase is not the best one ;) ). Anyway the implementation presented in the test is bad so we should think of the way to refactor it...
One of the ideas could be, assuming that the additional logic done in the Service is a part of the creation of the object, to move the code to the factory as such:
**PlayFactoryImplWithFieldSettingLogic.java**
```java
package com.blogspot.toomuchcoding.factory;
import com.blogspot.toomuchcoding.adapter.CommonPlayerAdapter;
import com.blogspot.toomuchcoding.adapter.DefencePlayerAdapter;
import com.blogspot.toomuchcoding.adapter.OffensivePlayerAdapter;
import com.blogspot.toomuchcoding.model.*;
/** * User: mgrzejszczak * Date: 09.06.13 * Time: 15:53 */public class PlayerFactoryImplWithFieldSettingLogic implements PlayerFactory {
    @Override    public Player createPlayer(PositionType positionType) {
        PlayerDetails player = createCommonPlayer(positionType);
switch (positionType) {
            case ATT:                return createOffensivePlayer(player);
case MID:                return createOffensivePlayer(player);
case DEF:                return createDefensivePlayer(player);
case GK:                return createDefensivePlayer(player);
default:                return new CommonPlayerAdapter(player);
}

}
    private Player createDefensivePlayer(PlayerDetails player) {
        DefencePlayerAdapter defencePlayerAdapter = new DefencePlayerAdapter(player);
defencePlayerAdapter.defend();
defencePlayerAdapter.playSomeMusic();
defencePlayerAdapter.doSomeSeriousCoding();
return defencePlayerAdapter;
}
    private OffensivePlayer createOffensivePlayer(PlayerDetails player) {
        OffensivePlayer offensivePlayer = new OffensivePlayerAdapter(player);
offensivePlayer.shoot();
return offensivePlayer;
}
    private PlayerDetails createCommonPlayer(PositionType positionType) {
        PlayerDetails playerDetails = new PlayerDetails();
playerDetails.setPosition(positionType);
return playerDetails;
}

}
```
In this way there is no casting the code is really clean. And now the PlayerService looks like this:
**PlayerServiceImplWIthoutUnnecessaryLogic.java**
```java
package com.blogspot.toomuchcoding.service;
import com.blogspot.toomuchcoding.factory.PlayerFactory;
import com.blogspot.toomuchcoding.model.*;
/** * User: mgrzejszczak * Date: 08.06.13 * Time: 19:02 */public class PlayerServiceImplWithoutUnnecessaryLogic implements PlayerService {
    private PlayerFactory playerFactory;
/**     * What's the point in having this method then?     * @param positionType     * @return     */    @Override    public Player playAGameWithAPlayerOfPosition(PositionType positionType) {
        return playerFactory.createPlayer(positionType);
}
    public PlayerFactory getPlayerFactory() {
        return playerFactory;
}
    public void setPlayerFactory(PlayerFactory playerFactory) {
        this.playerFactory = playerFactory;
}

}
```
And the question arises whether there is even any need to have such a method in your code base...
Summing it all up I hope that I managed to show how to:
- Use MockitoJUnitRunner to inject mocks in a clean way
- Use annotations or static methods to add extra interfaces that can be used by your mock
- Use BDDMockito to perform method stubbing
- Stub void methods with custom answer
- Stub and verify methods of the extra interfaces
- Refactor code that is using class casts
The sources are available at the
[TooMuchCoding Bitbucket](https://bitbucket.org/gregorin1987/too-much-coding/src/cab9f99626730e606c7ed354e81d141819351221/Unit%20Testing/Mockito%20-%20With%20Interfaces?at=default)
repository and
[TooMuchCoding Github](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Unit%20Testing/Mockito%20-%20With%20Interfaces)
repository.
