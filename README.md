# Wordle Solver

The most comprehensive, exhaustive, parameterized command-line *wordle* (<https://www.powerlanguage.co.uk/wordle/>) solver. Wordle is a really popular game made viral by it's inscrutable and quirky emoji-based game description.

The official Wordle game can have *2,315* candidate hidden words and *12,972* valid guessable words. The solver boasts a *100%* accuracy on all candidates. The optimal first guess is *SOARE* and the average number of attempts to a solution is *3.65*.

Features:

- Supports 5 modes: `play` (play Wordle against a CPU), `show` (to show a solution for a specific word), `solve` (to solve a puzzle online) or `solve_vision` (for auto-solving using OpenCV), `save` (to help you in the middle of a game you started) and `eval` (evaluate the performance)
- Mostly deterministic (we arbitrarily select the chronologically lowest word in tie-breakers)
- Highest accuracy of all solutions evaluated
- Support custom dictionaries with `--dict_file` and candidate files with `--cand_file` if different from the underlying dictionary.
- Support custom length wordles with `-N` and custom max guesses with `--guesses`.
- Supports "hard mode" where each guess must conform to previous hints with `--hard`.
- Fully tested
- Latency `~0.26s` per run on default ~9000 word dict and all 5 letter words.
- Now supports an extra mode called `gen_tree` which generates a tree for the entire solution set given a game and solver configuration.
Current dictionary used is the `--dict_file data/official_wordle_all.txt --cand_file data/official_wordle_common.txt`, which is the official Wordle setting.

Solver’s attempt to solve the Jan 10, 2022 wordle for the word `query`:

```markdown
⬛🟨⬛⬛🟨 AROSE
⬛🟩⬛⬛⬛ TUMID
⬛⬛🟨⬛⬛ GLYPH
🟩🟩🟩🟩🟩 QUERY
```

<img width="196" alt="Screen Shot 2022-01-12 at 12 26 16 AM" src="https://user-images.githubusercontent.com/1846373/149004246-6f200c36-de13-4bb3-90a6-eb34d27047ce.png">

# Algorithm

With the settings for non-strict play, using positional

- Find all candidates that fit the criteria.
- Amongst the valid candidates, compute a distribution of letters at each position.
- Find a guessable word from all valid guesses which optimizes sum(P(letter at pos i)) + 0.5 * sum(P letter not at pos i) amongst the candidate set. Break ties effectively.
- Repeat until there is only 1 candidate possible and guess it.

# Usage

### Play it yourself

`python main.py -m play`

```markdown
Guess? tares
TARES
⬛🟩🟨⬛⬛
Guess? unlit
UNLIT
⬛⬛⬛⬛⬛
Guess? raver
RAVER
🟨🟩🟩⬛🟩
Guess? favor
FAVOR
🟩🟩🟩🟩🟩
Solved! - favor
```

### Solve for an unknown word

`python main.py -m solve`

```
Try the word [TARES]. There are 8636 possible words: ['aahed', 'aalii', 'aargh', 'abaca', 'abaci']...
How did it do (0=⬛, 1=🟨, 2=🟩) e.g. 00000? 00000
Try the word [NOILY]. There are 575 possible words: ['biddy', 'biffy', 'bifid', 'bigly', 'bijou']...
How did it do (0=⬛, 1=🟨, 2=🟩) e.g. 00000? 02002
Try the word [DHOBI]. There are 39 possible words: ['bobby', 'boggy', 'booby', 'boogy', 'boomy']...
How did it do (0=⬛, 1=🟨, 2=🟩) e.g. 00000? 20100
Try the word [DODGY]. There are 3 possible words: ['dodgy', 'doggy', 'dowdy']...
How did it do (0=⬛, 1=🟨, 2=🟩) e.g. 00000? 22122
Try the word [DOGGY]. There are 1 possible words: ['doggy']...
How did it do (0=⬛, 1=🟨, 2=🟩) e.g. 00000? 22222
Solved! = doggy
```

### Show a solution for a specific word

`python main.py -m show -w oozed`

```
Word [OOZED]
Choosing [tares]. Total 8636 candidates: ['aahed', 'aalii', 'aargh', 'abaca', 'abaci']...
TARES
⬛⬛⬛🟩⬛
Choosing [coled]. Total 288 candidates: ['bedel', 'bedew', 'bevel', 'bezel', 'bided']...
COLED
⬛🟩⬛🟩🟩
Choosing [howdy]. Total 31 candidates: ['boded', 'boned', 'booed', 'bowed', 'boxed']...
HOWDY
⬛🟩⬛🟨⬛
Choosing [bipod]. Total 16 candidates: ['boded', 'boned', 'booed', 'boxed', 'domed']...
BIPOD
⬛⬛⬛🟨🟩
Choosing [dozen]. Total 8 candidates: ['domed', 'dozed', 'foxed', 'joked', 'mooed']...
DOZEN
🟨🟩🟩🟩⬛
Choosing [oozed]. Total 1 candidates: ['oozed']...
OOZED
🟩🟩🟩🟩🟩
Solved! - oozed
Woohoo! Solver solved it in 6 guesses!
```

### Solve for an unknown word using computer vision (NEW!)

`python main.py -m solve_vision`

[VIDEO REFERENCE](https://www.linkedin.com/feed/update/urn:li:activity:7022022029591592960/)

### Evaluate its performance

`python main.py -m eval -k 1000`

```

Evaluating on 1000 words
k=10: Failed: 1 Accuracy:90.00% Avg Time: 0.258s
k=20: Failed: 1 Accuracy:95.00% Avg Time: 0.250s
k=30: Failed: 1 Accuracy:96.67% Avg Time: 0.249s
k=40: Failed: 1 Accuracy:97.50% Avg Time: 0.244s
k=50: Failed: 1 Accuracy:98.00% Avg Time: 0.239s
...
k=970: Failed: 10 Accuracy:98.97% Avg Time: 0.236s
k=980: Failed: 10 Accuracy:98.98% Avg Time: 0.236s
k=990: Failed: 10 Accuracy:98.99% Avg Time: 0.236s
Failed on: ['jived', 'hides', 'razer', 'zooks', 'jills', 'gibed', 'wises', 'yipes', 'wipes', 'sises']
Distribution of remaining candidates: [(4, 5), (3, 3), (2, 1), (5, 1)]
K=999: Failed: 10 Accuracy:99.00%

```

### Run Tests

`python -m unittest` runs the entire test suite.

# Advanced Usage

### Custom settings

Here are things you can customize with each run:

- `-d` Specify a debug level. `2` gives the most details, and `1`, the default if this flag is specified gives certain details like the length of the candidate set and what the previous clues tell us.
- `-N` Specify the length of the words in the wordle you want to play. Default is `5`.
- `--guesses` Specify the number of valid guesses. Default is `6`.
- `-hard` Whether or not to play on "hard mode" where each subsequent guess must adhere to the previous clues.
- `--dict_file` The word set you want to use. Details below.

### Specifying a dict file

Results of the evaluation and performance of the eval depend greatly on the choice of dict file used. Here are some options provided by default. You can specify it (or add your own) with `--dict_file`

- `data/dictionary_proper.txt`: 8636 5-letter words. A valid Scrabble dictionary, and the default choice. Source: <https://github.com/zeisler/scrabble>
- `data/words_alpha.txt`: 15918 5-letter words. Contains strange words like `chivw`. Source: <https://github.com/dwyl/english-words>
- `data/unix_words.txt`: 8497 lowercase 5-letter words. Source: Default `/usr/share/dict/words` on Mac machines.
- `'data/lexicon_4958.txt`: 4958 5-letter words. Source: @dsivakumar's <https://github.com/aravinho/wordle_public/blob/main/wordle/lexicons/lexicon_4958>. Explanation on how this is derived is in the repo.
- `data/sgb-words.txt`: 5757 5-letter words. The list of 5-letter words from Knuth's Stanford Graph Base. Source: <https://www-cs-faculty.stanford.edu/~knuth/sgb-words.txt>
- `data/five_letter_common.txt`: 2499 common 5-letter words. This is the same word list used in <https://swag.github.io/evil-wordle/>
- `data/official_wordle_all.txt`: All 12972 official valid 5-letter words taken from the Wordle website source <https://www.powerlanguage.co.uk/wordle/main.c1506a22.js>
- `data/official_wordle_common.txt`: The official 2315 "common" guessable 5-letter words taken from the Wordle website source <https://www.powerlanguage.co.uk/wordle/main.c1506a22.js>
- `data/past_wordles_200.txt`: The official 200 first wordle words. Not meant for use as a dictionary.

The official Wordle game uses a large lexicon for valid guess words, but a smaller subset for valid magic words. We ignore this assumption and assume any valid word can be guessed.

# Evaluation

### Official Wordle

The official Wordle game can have *2,315* candidate hidden words and *12,972* valid guessable words. The solver boasts a *100%* accuracy on all candidates. The optimal first guess is *SOARE* and the average number of attempts to a solution is *3.65*. The distribution of number of attempts is:

- Two: 67 (2.9%)
- Three: 914 (39.5%)
- Four: 1115 (48.2%)
- Five: 208 (9.0%)
- Six: 11 (<1%)

Note: I believe @npinsker's full Rust brute force solution shared on Twitter achieves a 3.47 average attempts, and starts with *SOARE*.

On the first 220 real world Wordles, every word was solved with an average number of attempts of *3.69* with `jaunt` consistently taking 6 attempts.

With `--gen_tree`, in ~30mins on the official Wordle data we can explore every single avenue with which to play the game. Because the underlying solver is deterministic, this is essentially a cached version of the solver's solution given certain solver settings. We generated `tree/solution_tree.pickle` which is ~125KB and stores the moves to guess all 2315 possible words and support using these with `--tree_file`. This tree file can then be loaded up as a small drop-in replacement to solve Wordles online. Some statistics on the solution:

- In every variant of the game, only `2,677` of total `12,972` guessable words were used (20.7% of words used).
- The max dept of the tree is 6, for 11 nodes. The average depth is 3.68.
- The starting node is `soare` after which there are 127 of the possible (3^5 = 243) valid clues Wordle can return.
- After `soare`, the most likely outcomes are:
  - ⬛⬛⬛⬛⬛ 8%, or 183/2315 time, the next guess is `linty`. Average attempts in this path: `4.09`
  - ⬛⬛🟨⬛⬛ 6%, or 138/2315 time, the next guess is `canty`. Average attempts in this path: `4.09`
  - ⬛⬛⬛⬛🟨 5.2%, or 120/2315 time, the next guess is `inlet`. Average attempts in this path: `3.84`
  - ⬛⬛⬛🟨🟨 5%, or 120/2315 time, the next guess is `cider`. Average attempts in this path: `4.14`
  - ⬛🟩⬛⬛⬛ 3.7%, or 87/2315 time, the next guess is `bludy`. Average attempts in this path: `4.11`

### Past Wordles

As of Jan 24, 2022, on the past 220 wordles, it solves all of them with an average attempts of `3.84`. The distribution of number of attempts is:

- Two: 1 (<1%)
- Three: 58 (26%)
- Four: 115 (52%)
- Five: 24 (11%)
- Six: 2 (<1%)

The word list is available at `data/past_wordles_220.txt`.

### On 8636 Scrabble Words list

Using a dictionary of scrabble words, there are 172,819 total words and around 5% of them are exactly 5 letters long (8,636). The algorithm devised achieves a *99.35%* success rate at guessing the right word, failing to get the correct the answer for 56 words.

The 56 failure cases are `sakes`, `mooed`, `jived`, `wanes`, `jocks`, `minks`, `wades`, `jaded`, `zoner`, `joker`, `wived`, `jakes`, `mozos`, `goxes`, `vills`, `rover`, `zooks`, `cozes`, `jibes`, `wakes`, `hajes`, `joked`, `sinhs`, `zaxes`, `yaffs`, `hiker`, `bases`, `moved`, `bises`, `zills`, `hided`, `eaved`, `vined`, `surfs`, `jiber`, `gibed`, `dozer`, `fuzed`, `mixed`, `boxed`, `waxes`, `waves`, `vomer`, `egged`, `mazed`, `pests`, `hived`, `socks`, `fazes`, `vests`, `jibed`, `mewed`, `hazes`, `sooks`, `woods`, `sinks`
For all these words, there are 2-5 candidate words left at the last guess, and with a random last guess, there is a probability of guessing these too.

The average number of attempts is around *4-4.1*, and the ideal starting word is *TARES*.

Other settings achieved:

- Global character frequency heuristic: Couldn't solve for 133 out of 1000 random samples (86.7% Success rate)
- Conditional character frequency heuristic, on candidates left: Couldn't solve for 100 out of 1000 random samples (90.0% Success rate)
- Non-strict solution: Couldn't solve for 46 out of 1000 random samples (95.4% Success rate)
- Position-aware frequency heuristic + bug fixes: Couldn't solve for 9 out of 1000 random samples (99.1% Success rate)

### Different Word Lengths

Using other values of `N` with `MAX_GUESSES=6`, with the optimal solver:

- N=2 (96 words) `K=96: Failed: 32 Accuracy:66.67% Avg Attempts: 2.67 Avg Time: 0.002s`
- N=3 (972 words) `K=100: Failed: 23 Accuracy:77.00% Avg Attempts: 3.69 Avg Time: 0.030s`
- N=4 (3903 words) `K=100: Failed: 5 Accuracy:95.00% Avg Attempts: 4.46 Avg Time: 0.116s`
- N=5 (8636 words) `K=100: Failed: 1 Accuracy:99.00% Avg Attempts: 4.13 Avg Time: 0.241s`
- N=6 (15232 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 3.83 Avg Time: 0.471s`
- N=7 (23109 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 3.58 Avg Time: 0.678s`
- N=8 (28420 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 3.25 Avg Time: 0.819s`
- N=9 (24873 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 3.00 Avg Time: 0.728s`
- N=10 (20300 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 2.86 Avg Time: 0.643s`
- N=11 (15504 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 2.64 Avg Time: 0.468s`
- N=12 (11357 words) `K=100: Failed: 0 Accuracy:100.00% Avg Attempts: 2.45 Avg Time: 0.327s`

### Evil Wordle

The solver's solution to [Evil Wordle](https://swag.github.io/evil-wordle/) is in 5 tries. It takes 5 tries in hard mode as well. I believe the minimum you can go is a 4-ply solution, but it's not necessary that the best Evil Wordle solver is the most accurate Wordle solver.

<img width="248" alt="Screen Shot 2022-01-12 at 5 33 09 PM" src="https://user-images.githubusercontent.com/1846373/149136925-39e5c80f-9a5a-479b-a807-de654cabc432.png">

For 6-letters, here's the 5-ply solution. In hard mode, I've found a 7-ply solution.

<img width="283" alt="Screen Shot 2022-01-12 at 5 42 19 PM" src="https://user-images.githubusercontent.com/1846373/149138177-cd044202-6cd4-4dcd-a203-d8c5f1052a8e.png">

# Future Work

- This solution does pretty well and generalizes to various dictionaries, but the optimal solution is to fully generate the mini-max tree for a given dictionary:
  - Demo: <http://www.npinsker.me/puzzles/wordle/>
  - Code (Rust): <https://gist.github.com/npinsker/a495784b9c6eacfe481d8e38963b335c>
  - Tweet: <https://twitter.com/npinsker/status/1478981155529519104>
- Bug: Correctly support letter out of place when dealing with words with two repeating letters
- Expose into a web UI solver in a static UI.

# Changelog

- 3.1 (Jan 20) "Save" mode
- Adds `-m save` which allows you to input your gameplay to any point and recommends the best word for you to salvage your game, as well as candidates remaining.
- 3.0 (Jan 19) The "Official" 100% Wordle release
  - Add official Wordle dicts, report 100% number on all official Wordle words.
  - Support separate candidate and guess dictionaries (`--dict_file` and `--cand_file`) just like the official game.
  - Support generating and playing with a cached solution tree (almost instant!)
  - Make solver fully deterministic.
  - Support emoji input in `solve` mode.
  - Support `--eval_out_file` to be able to explore the results of an eval.
- 2.0 (Jan 12) The "Flexible" release
  - Support custom dictionaries used by online wordle solvers with proper documentation and  add `--dict_file` to provide a custom dictionary.
  - Support customizing `--N`, the number of guesses, and `--max_guesses`, the max guesses for a game.
  - Better handle exceptions in edge cases where no candidates exist.
  - Parameterize non pos weight.
  - Add options for `--hard` mode, `--debug` to understand what's going on better.
  - Better solving logic: optimize explore guessing, better char selection when duplicate chars.
- 1.0 (Jan 9) The "Working" release
  - Supports various different modes and achieves a `99.35%+` success rate on the `8636` valid 5 letter scrabble words.
  - Allows you to `-m play`, `-m solve`, `-m show` a solution for a word and `-m eval` to evaluate the logic across various words.
