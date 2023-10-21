# Testing

This portfolio entry shows my understanding and skills in testing based on the practical exercise "test battles". In this challenge, we were given a template repository with a partially completed MAUI application for a Hangman game. Each team had to implement the required methods and create unit tests for them. The unit tests were created using the xUnit framework. After the challenge, two teams were cross-tested, and unit tests of one team tested the application code of another team. 

I will detail two unit tests that I wrote during this exercise for two methods. I believe these instances effectively illustrate my approach to testing.

## Unit tests

#### First method

The first method I tested was the `SelectWord` method, designed to select a random word for the user to guess, based on the selected difficulty. The method returns the word chosen for the game and accepts a single string parameter, `gameType`, which specifies the chosen difficulty level. It uses switch statements to retrieve a random word from a predefined list corresponding to the difficulty level. The word lists are predefined in a separate class, `HangmanWords.cs`.

##### Test code

```
using Hangman;
using Hangman.Models;


namespace UnitTestProject
{
    public class TestSelectWord_v2
    {
        // Test SelectWord method, checks if return valid game word based on difficulty level
        [Fact]
        public void TestSelectWord_ReturnsWordListBasedOnLevel()
        {
            var gamePage = new GamePage("Easy");
            string selectedWord = gamePage.SelectWord("Easy");

            Assert.Contains(selectedWord, HangmanWords.EasyWords);
        }
    }
}
```



Before I started testing the `SelectWord` method, I added the using directive `Hangman.Models` and changed `HangmanWords.cs` class to public to access this class in my unit test.

The test utilizes the [Fact] attribute to verify if the `SelectWord` method returns a word from the predefined `HangmanWords.EasyWords` list found in the `HangmanWords.cs` class, based on the selected difficulty. Firstly, an instance of the `GamePage` object is created, specifying "Easy" as the difficulty level. The `GamePage` class is responsible for managing the game logic.

After instantiation, the `SelectWord` method is invoked with "Easy" as the argument, and the method's return value is stored in the variable `selectedWord`. Following this, an xUnit assertion checks whether the `HangmanWords.EasyWords` list contains the `selectedWord`.

Testing this method is crucial for the game's functionality and user experience, as it ensures that the game logic correctly retrieves a word from the appropriate list corresponding to the chosen difficulty level, a fundamental aspect of the game.


#### Second method

The second method I tested was the `ResetDisplay` method, which is responsible for resetting the game's display to the initial image and change the appropriate number of visible labels at the start of a new game or round. It takes a single parameter, `word`, which is the word players need to guess. The method operates within a loop that iterates through the first 12 characters of the `word`. For each iteration, the method checks if the current position is within the length of the `word`. If it is, the method retrieves the character at that position and updates the corresponding label in the user interface. That makes new game ready, with partially discovered word based on the difficulty level. 

##### Test code

```
using Hangman;

namespace UnitTestProject
{
    public class TestResetDisplay
    {
        // Test ResetDisplay method, checks if labels are set correctly 
        [Fact]
        public void TestResetDisplay_ReturnsChangedLabel()
        {
            var gamePage = new GamePage("Easy");
            gamePage.ResetDisplay("apple");

            Label letter1 = gamePage.FindByName<Label>("Letter1");
            Label letter2 = gamePage.FindByName<Label>("Letter2");


            Assert.Equal("a", letter1.Text);
            Assert.Equal("p", letter2.Text);
        }
    }
}
```
The test utilizes the [Fact] attribute, to verify a specific aspect of the method functionality, in this case, whether the `ResetDisplay` method correctly updates the corresponding labels in the UI.

First, the test initializes an instance of the `GamePage` class, setting "Easy" as the difficulty level. 

The test invokes the `ResetDisplay` method with "apple" as the argument, simulating the start of a new game where "apple" is the word to guess. The method is expected to update the labels representing the letters of the new word.

The main focus here is on the first two letters of "apple," represented by `letter1` and `letter2`. The test retrieves these labels from the UI and checks their text properties. Next, assertions validate that these labels reflect the characters 'a' and 'p', respectively.

Testing this method is important confirming that the game's visual setup correctly corresponds to the new game state, ensuring consistency and reliability in the gameplay experience.

##### Run tests 

Both my tests were created as a seperate classes with meaningful names describig which method is were tested and what it re for better readibility and 

<figure>
  <img src="https://github.com/patryklbn/portfolio-assessment/blob/master/images/28.png?raw=true" alt="Unit Test Explorer">
  <figcaption><b>Fig.1 - Unit Tests Explorer - All tests passed</b></figcaption>
</figure>

#### Evaluation
During cross-testing, my unit test TestResetDisplay_ReturnsChangedLabel was used to test the code from other teams. In all cases, my method failed, resulting in an error:

```
UnitTestProject.TestResetDisplay.TestResetDisplay_ReturnsChangedLabel [FAIL]
[20/10/2023 4:00:13.609 PM]  [xUnit.net 00:00:00.23]       Assert.Equal() Failure: Strings differ
[20/10/2023 4:00:13.609 PM]  [xUnit.net 00:00:00.23]       Expected: "a"
[20/10/2023 4:00:13.610 PM]  [xUnit.net 00:00:00.23]       Actual:   null
[20/10/2023 4:00:13.611 PM]  [xUnit.net 00:00:00.23]       Stack Trace:
[20/10/2023 4:00:13.611 PM]  [xUnit.net 00:00:00.23]         C:\Users\40000812\source\repos\green\OrangeTests\TestResetDisplay.cs(18,0): at UnitTestProject.TestResetDisplay.TestResetDisplay_ReturnsChangedLabel()
[20/10/2023 4:00:13.612 PM]  [xUnit.net 00:00:00.23]            at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)
[20/10/2023 4:00:13.613 PM]  [xUnit.net 00:00:00.23]            at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)
[20/10/2023 4:00:13.614 PM] [Error] [xUnit.net 00:00:00.25]
```

The error clearly shows that instead of the expected string "a," my test retrieved a "null" value. Reflecting on this, I believe there could be a couple of reasons behind the test failure:

**Unfinished Method**: Our team's ResetDisplay method wasn't fully completed. It was set up to change the state of the word labels but didn't handle the reset of images. This partial functionality might have impacted the test, especially since the other teams might have had a more comprehensive implementation of the method.

**Different Implementation Approaches**: In my test, I assumed labels would be accessed and modified in a certain way, specifically through something like a FindMyLabel method. However, the other teams might have approached this functionality differently, leading to a mismatch between my test assumptions and their code.

**Conclusion**: The cross-testing phase of this challenge led me to conclude that testing requires a deeper understanding of the code being tested. Even if my test passed on our team's code, it's crucial to test finished functionality, especially when dealing with UI elements, to avoid inconsistencies. This experience underscores the importance of crafting more versatile tests that are adaptable to different approaches.

