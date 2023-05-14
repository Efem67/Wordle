<script>
    import wordlist from "word-list-json";
    import Header from "../components/Header.svelte";
    import GameBoard from "../components/GameBoard.svelte";
    import Keyboard from "../components/Keyboard.svelte";
    import GameOptionsMenu from "../components/GameOptionsMenu.svelte";
    import EndInfo from "../components/EndInfo.svelte";
    import { select_options } from "svelte/internal";

    let hintArray = [];
    let gameResult = "";
    let gameEnded = false;
    let gameStarted = false;
    let wantHint = false;
    let wordLength = undefined;
    let hiddenWord = undefined;
    let wordsDictionary;
    let chances;
    let whichRow = 0;
    let gameBoardArr = [];
    let keyRows = {
        firstRow: ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
        secRow: ["A", "S", "D", "F", "G", "H", "J", "K", "L"],
        thirdRow: ["ENTER", "Z", "X", "C", "V", "B", "N", "M", "BACKSPACE"],
    };
    let fromJson = false;

    let lettersOnGoodPos = ["H"];
    let lettersOnlyInWord = [];
    let usedLetters = [];

    const setOptions = (detail) => {
        hintArray = [];
        wantHint = false;
        fromJson = false;
        gameEnded = false;
        gameBoardArr = [];
        whichRow = 0;
        gameStarted = true;
        lettersOnGoodPos = [];
        lettersOnlyInWord = [];
        usedLetters = [];

        wordLength = detail.wordsLength;
        chances = wordLength + 1;
        for (let i = 0; i < chances; i++) {
            let row = [];
            for (let j = 0; j < wordLength; j++) {
                row.push({
                    value: 0,
                    color: "bg-white",
                    border: "border-gray-300",
                });
            }
            gameBoardArr.push(row);
        }

        for (let i = 0; i < wordLength; i++) {
            hintArray.push(0);
        }

        if (detail.file) {
            wordsDictionary = detail.file[wordLength];
            fromJson = true;
        } else {
            wordsDictionary = wordlist.filter((item) => {
                if (item.length == Number(wordLength)) return item;
            });
        }
        hiddenWord =
            wordsDictionary[Math.floor(Math.random() * wordsDictionary.length)];
        console.log("HIDDEN_WORD", hiddenWord);
    };

    const clickIntoKeys = (key) => {
        let arr = gameBoardArr[whichRow];
        let isFull = !arr.some((el) => el.value == 0);
        let zeroIndex = arr.findIndex((el) => el.value === 0);
        if (key == "ENTER") {
            if (isFull) checkWordIsCorrect();
        } else if (key == "BACKSPACE") {
            if (arr[0].value != 0) {
                if (isFull) {
                    arr[arr.length - 1].value = 0;
                } else {
                    arr[zeroIndex - 1].value = 0;
                }
            }
        } else {
            if (isFull) return;
            arr[zeroIndex].value = key;
        }
        gameBoardArr = gameBoardArr;
    };

    const checkWordIsCorrect = () => {
        let wordInLine = "";
        gameBoardArr[whichRow].forEach((el) => (wordInLine += el.value));

        if (!fromJson) {
            if (!wordsDictionary.includes(wordInLine.toLowerCase())) {
                return;
            }
        }

        gameBoardArr[whichRow].map((el, i) => {
            let onGoodPos = false;
            if (el.value == hiddenWord[i].toUpperCase()) {
                el.color = "bg-green-400";
                onGoodPos = true;
                lettersOnGoodPos = [...lettersOnGoodPos, el.value];
            } else {
                el.color = "bg-gray-200";
                usedLetters = [...usedLetters, el.value];
            }

            if (
                !onGoodPos &&
                hiddenWord.toUpperCase().indexOf(el.value) != -1
            ) {
                el.color = "bg-yellow-400";
                lettersOnlyInWord = [...lettersOnlyInWord, el.value];
            }
        });

        console.log(wordInLine.toUpperCase());
        console.log(hiddenWord.toUpperCase());
        if (wordInLine.toUpperCase() == hiddenWord.toUpperCase())
            endGameFunc("Win");
        else if (whichRow + 1 == chances) endGameFunc("Fail");

        whichRow++;
    };

    const endGameFunc = (result) => {
        gameResult = result;
        gameEnded = true;
    };

    const hintToggle = () => {
        if (wantHint) return;
        wantHint = true;

        let howManyLetters = Math.floor(wordLength / 2);
        let indexesArr = [];

        while (indexesArr.length < howManyLetters) {
            let random = Math.floor(Math.random() * wordLength);
            if (!indexesArr.includes(random)) {
                indexesArr.push(random);
            }
        }
        indexesArr.forEach((index) => {
            hintArray[index] = hiddenWord.toUpperCase()[index];
        });

        hintArray = hintArray;
        chances = whichRow + 1;
        console.log(chances);

        for (let i = chances; i < gameBoardArr.length; i++) {
            for (let j = 0; j < wordLength; j++) {
                gameBoardArr[i][j].border = "border-gray-100";
            }
        }
        gameBoardArr = gameBoardArr;
    };
</script>

<div
    class="w-full h-screen bg-gray-white flex flex-col items-center overflow-hidden"
>
    {#if gameEnded}
        <EndInfo
            {gameResult}
            {hiddenWord}
            bind:startNewGame={gameStarted}
            bind:stillOpenGameInfo={gameEnded}
        />
    {/if}
    {#if !gameStarted}
        <GameOptionsMenu
            on:setGamePlayOptions={({ detail }) => setOptions(detail)}
        />
    {:else}
        <Header
            {hintArray}
            bind:gameStarted
            on:clickIntoHint={({ detail }) => hintToggle()}
        />
        <GameBoard array={gameBoardArr} />

        <Keyboard
            {gameEnded}
            {keyRows}
            bind:lettersOnGoodPos
            bind:lettersOnlyInWord
            bind:usedLetters
            on:keyboardClick={({ detail }) => clickIntoKeys(detail)}
        />
    {/if}
</div>
