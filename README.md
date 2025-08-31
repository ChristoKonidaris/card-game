# Technical Assessment

Provided by B-Sure: https://bsure.co.za/

Instructions for this assessment:

- 6 players are dealt 5 cards from two 52 card decks, and the winner is the one with the highest
score.
The score for each player is calculated by adding up all 5 card values for each player, where the
number cards have their face value, J = 11, Q = 12, K = 13 and A = 11 (not 1).
- In the event of a tie, the scores are recalculated for only the tied players by calculating a "suit
score" for: 
- o Each player to see if the tie can be broken (it may not).
- o Each card is given a score based on its suit, with diamonds = 1, hearts = 2, spades = 3 and
clubs = 4, and the player's score is the multiplication of all 5 suit value.

You are required to write an application using a stack of your choice that needs to do the following:
- Run on Windows.
- Randomly generate the 5 cards that each player will receive. Show the cards that each player
received, the winner(s) and the score for each player.
- Handle any exceptions.

Frontend interface: https://card-game-dev.vercel.app/

Tech stacks used:
- Vue 3
- Javascript
- TailwindCSS
- Vercel (hosting)

To simulate a tie, press shift + D to toggle the force tie button.
