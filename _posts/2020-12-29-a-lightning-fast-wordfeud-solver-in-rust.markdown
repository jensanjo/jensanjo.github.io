---
layout: post
title:  "A lightning fast Wordfeud Solver in Rust"
date:   2020-12-29 14:38:21 +0100
categories: wordfeud rust
---

A few years ago I became intrigued by the Rust programming language. 
Like many other people I enjoy playing the popular [Wordfeud](https://wordfeud.com/) game. I was looking for a programming project to exercise my Rust skills, and creating a wordfeud solver in Rust seemed like a nice fit. 

# The Wordfeud game

The Wordfeud game is derived from Scrabble. The objective of the game is to put words on the 15x15 game board with the letter tiles you have. You score points based on the value of each tile, and the type of the square where the tile is placed.
In addition to the regular tiles you can also get a blank tile that can be assigned any letter when put on the board, but has a value of 0 points.

<img src="https://github.com/jensanjo/wordfeud-ocr/raw/master/lib/tests/screenshot_english.png" alt="screenshot" width="240">

Wordfeud can be played in different languages (English, German, French, Spanish, Dutch, Swedish, Norwegian, Danish, Portuguese and Finnish). Each language has a different tile distribution. The details of the game are explained [here](https://wordfeud.com/wf/help/).

# A Wordfeud Solver

The initial goal for my Wordfeud Solver was to create a Rust library that can be used to build a Wordfeud player.

* Read a wordlist for the selected language
* Given the state of the game board and a set of tiles, calculate all possible words that can be played
* Calculate the scores for all words and return the top scores

# Wordfeud Solver algorithm

The solver presented here is based on the excellent [wordfeudplayer](https://github.com/mrcz/Wordfeud-Player) Python package.


## The wordlist

The wordlist is stored in a [trie](https://en.wikipedia.org/wiki/Trie) data structure. A trie is a search tree where each node has an associated label. 
All the descendants of a node have a common prefix. The root node is the empty string.

This example stores the words `ab`, `aba`, `ax`, `axe`, `ba`, `bar`, `bad`:

![example wordlist](/assets/img/wordlist.png)

The bold nodes are `terminal`, which means they represent a valid word. Non-terminal nodes represent a valid prefix.

## The board

The wordfeud board can be thought of as a square grid of 15x15 cells, where each cell can be empty or contain a tile.

|    | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10| 11| 12| 13| 14|
|----|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|  0 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|  1 |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
|  2 |   |   |   |   |   |   |   |   |   |   |   |   | Z |   |   |
|  3 |   |   |   |   |   |   |   |   |   |   |   |   | I | F |   |
|  4 |   |   |   |   |   |   |   |   |   | D | E | N | T | A | L |
|  5 |   |   |   |   |   |   |   |   |   |   | V |   | E | X |   |
|  6 |   |   |   |   |   |   |   | H |   |   | E |   |   |   |   |
|  7 |   |   |   |   |   |   | H | E | D | O | N | I | C |   |   |
|  8 |   |   |   |   | R |   |   | D |   |   | L |   |   |   |   |
|  9 |   |   |   |   | O |   |   | O |   |   | Y |   |   |   |   |
| 10 |   |   |   |   | B | R | E | N | T |   |   |   |   |   |   |
| 11 |   |   |   |   | O |   |   | I |   |   | V |   |   |   |   |
| 12 |   | G | A | I | T | S |   | S |   |   | E |   |   |   |   |
| 13 |   |   |   |   | I |   |   | M | U | N | G | E | D |   |   |
| 14 |   |   |   |   | C |   |   |   |   |   | A |   |   |   |   |




