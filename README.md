## Guess the Word Game

### The challenge:

Building a word game with HTML, CSS and JavaScript by utilizing an API call to retrieve a random word from a Skillcrush text file that contains 800 of the most common words.

Players start by entering a letter. If they guess correctly, the letter appears in the word. If they guess incorrectly, they learn how many guesses they have remaining. The player can also see which letters they’ve already guessed. The game lets the player know if they’ve already guessed a letter or entered a non-alphabetic character.

For now, the game is set up so that the players have 8 guesses to solve the game.

![screenshot](img/screenshot.png)

### JS code challenges

#### Creating a placeholder for each letter of the word, represented by circles ("●"), ensuring the word remains hidden until guessed:

```js
const placeholderLetters = [];

for (const letter of word) {
    placeholderLetters.push("●");
}

wordInProgress.innerText = placeholderLetters.join("");
};
```

#### An event listener is attached to the Guess button, triggering the makeGuess function. To prevent the form from submitting and refreshing the page, e.preventDefault() is included.

```js
guessBtn.addEventListener("click", function (e) {
  e.preventDefault();
  message.innerText = "";
  const guess = letterInput.value;

  const goodGuess = validateInput(guess);

  if (goodGuess) {
    makeGuess(guess);
  }

  letterInput.value = "";
});
```

#### The validateInput function checks the player's input to ensure it meets certain criteria, such as being a single alphabetic letter. Error messages are displayed to guide the player's input.

```js
const validateInput = function (input) {
  const acceptedLetter = /[a-zA-Z]/;
  if (input.length === 0) {
    message.innerText = "Please enter a letter";
  } else if (input.length > 1) {
    message.innerText = "Please enter a single letter";
  } else if (!input.match(acceptedLetter)) {
    message.innerText = "Please enter a letter from A to Z";
  } else {
    return input;
  }
};
```

#### Creating a function that accepts a letter as the parameter:

```js
const makeGuess = function (guess) {
  guess = guess.toUpperCase();
  if (guessedLetters.includes(guess)) {
    message.innerText = "You already guessed this letter. Try again.";
  } else {
    guessedLetters.push(guess);
    updateGuessesRemaining(guess);
    showGuessedLetters();
    updateWordInProgress(guessedLetters);
  }
};
```

#### The `showGuessedLetters` function dynamically creates list items for each guessed letter and appends them to the guessed letters list element (`guessedLettersEl`), allowing players to track their guesses.

```js
const showGuessedLetters = function () {
  guessedLettersEl.innerHTML = "";
  for (const letter of guessedLetters) {
    const li = document.createElement("li");
    li.innerText = letter;
    guessedLettersEl.append(li);
  }
};
```

#### The updateWordInProgress function updates the displayed word, revealing correctly guessed letters while hiding the remaining letters as placeholders represented by circles ("●"). Additionally, it checks for win conditions by calling the showWin function.

```js
const updateWordInProgress = function (guessedLetters) {
  const wordUpper = word.toUpperCase();
  const wordArray = wordUpper.split("");
  const revealWord = [];

  for (const letter of wordArray) {
    if (guessedLetters.includes(letter)) {
      revealWord.push(letter.toUpperCase());
    } else {
      revealWord.push("●");
    }
  }
  wordInProgress.innerText = revealWord.join("");

  showWin();
};
```

#### The showWin function compares the guessed word (converted to uppercase) with the current state of the word in progress. If they match, it indicates that the player has successfully guessed the word, displaying a congratulatory message and triggering the startOver function.

```js
const showWin = function () {
  if (word.toUpperCase() === wordInProgress.innerText) {
    message.classList.add("win");
    message.innerHTML = `<p class="highlight">You guessed the correct word! Congrats!</p>`;

    startOver();
  }
};
```

#### The updateGuessesRemaining function updates the remaining guess count and provides feedback on the player's guesses. If the player runs out of guesses, the game ends and the startOver function is called.

```js
const updateGuessesRemaining = function (guess) {
  const upperWord = word.toUpperCase();
  if (!upperWord.includes(guess)) {
    message.innerText = `The word has no ${guess}`;
    remainingGuesses -= 1;
  } else {
    message.innerText = `Yes, the word contains ${guess}`;
  }

  if (remainingGuesses === 0) {
    message.innerHTML = `Game over! The word was <span class="highlight">${word}</span>`;
    startOver();
  } else if (remainingGuesses === 1) {
    remainingGuessesSpan.innerText = `${remainingGuesses} guess`;
  } else if (remainingGuesses > 1) {
    remainingGuessesSpan.innerText = `${remainingGuesses} guesses`;
  }
};
```

#### The startOver function hides and shows various elements in order to reset the game interface:

```js
const startOver = function () {
  playAgainBtn.classList.remove("hide");
  guessBtn.classList.add("hide");
  remainingGuessesEl.classList.add("hide");
  guessedLettersEl.classList.add("hide");
};
```

#### Adding an event listener to the Play Again button to reset the game when the button is clicked:

```js
playAgainBtn.addEventListener("click", function () {
  message.classList.remove("win");
  message.innerText = `Try again!`;
  guessedLetters = [];
  remainingGuesses = 8;
  remainingGuessesSpan.innerText = `${remainingGuesses} guesses`;
  guessedLettersEl.innerHTML = "";

  getWord();

  playAgainBtn.classList.add("hide");
  guessBtn.classList.remove("hide");
  remainingGuessesEl.classList.remove("hide");
  guessedLettersEl.classList.remove("hide");
});
```
