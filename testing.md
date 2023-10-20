# Testing

This portfolio entry shows my understanding and skills in testing based on the practical exercise "test battles". In this challenge, we were given a template repository with a partially completed MAUI application for a Hangman game. Each team had to implement the required methods and create unit tests for them. The unit tests were created using the xUnit framework. After the challenge, two teams were cross-tested, unit tests of one team and test code of another.

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



Before I started testing the `SelectWord` method, I added the using directive `Hangman.Models` and changed `HangmanWords.cs` to public to access this class in my unit test

The test utilizes the [Fact] attribute to verify if the `SelectWord` method returns a word from the predefined `HangmanWords.EasyWords` list found in the HangmanWords.cs class, based on the selected difficulty. Firstly, an instance of the `GamePage` object is created, specifying "Easy" as the difficulty level. The `GamePage class` is responsible for managing the game logic.

After instantiation, the `SelectWord` method is invoked with "Easy" as the argument, and the method's return value is stored in the variable `selectedWord`. Following this, an xUnit assertion checks whether the `HangmanWords.EasyWords` list contains the `selectedWord`.

Testing this method is crucial for the game's functionality and user experience, as it ensures that the game logic correctly retrieves a word from the appropriate list corresponding to the chosen difficulty level, a fundamental aspect of the game.


#### Second method

The second method I tested was the `ResetDisplay` method, which is responsible for resetting the game's display at the start of a new game or round. It takes a single parameter, `word`, which is the word players need to guess. The method operates within a loop that iterates through the first 12 characters of the `word`. For each iteration, the method checks if the current position is within the length of the `word`. If it is, the method retrieves the character at that position and updates the corresponding label in the user interface. 

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

