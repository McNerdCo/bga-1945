# Deck Design

This document is to help think through how to structure the deck. Note that the BGA framework provides a fair bit of common utility function for decks of cards that we'll want to take advantage of. See the docs here: [https://boardgamearena.com/doc/Deck](https://boardgamearena.com/doc/Deck)

## Database Structure 

I think we should be able to use the standard DB structure for a deck:

```sql
CREATE TABLE IF NOT EXISTS `card` (
  `card_id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `card_type` varchar(16) NOT NULL,
  `card_type_arg` int(11) NOT NULL,
  `card_location` varchar(16) NOT NULL,
  `card_location_arg` int(11) NOT NULL,
  PRIMARY KEY (`card_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;
```

- `card_id`: unique and auto-generated
- `card_type`: the general type of the card, and should correspend to its folder or subfolder name (`army`, `navy`, `airforce`, `marines` `special`, `northamerica`, `southamerica`, `africa`, `asia`, `australia`, `europe`, `victory`)
- `card_type_arg`: An integer that will represent the specific card within the type
    - for attack cards, this represents the strength (1-5)
    - for special cards, this represents a specific special card
    - for territory cards, this corresponds to particular territory within the continent
    - not used for victory cards

With the DB defined in this way, our `createCards` function would look something like this:

```php
$cards = array(
    array( 'type' => 'army', 'type_arg' => 1, 'nbr' => 5 ),
    array( 'type' => 'army', 'type_arg' => 2, 'nbr' => 4 ),
    array( 'type' => 'army', 'type_arg' => 3, 'nbr' => 3 ),
    array( 'type' => 'army', 'type_arg' => 4, 'nbr' => 2 ),
    array( 'type' => 'army', 'type_arg' => 5, 'nbr' => 1 ),
    array( 'type' => 'navy', 'type_arg' => 1, 'nbr' => 5 ),
    array( 'type' => 'navy', 'type_arg' => 2, 'nbr' => 4 ),
    array( 'type' => 'navy', 'type_arg' => 3, 'nbr' => 3 ),
    array( 'type' => 'navy', 'type_arg' => 4, 'nbr' => 2 ),
    array( 'type' => 'navy', 'type_arg' => 5, 'nbr' => 1 ),
    array( 'type' => 'airforce', 'type_arg' => 1, 'nbr' => 5 ),
    array( 'type' => 'airforce', 'type_arg' => 2, 'nbr' => 4 ),
    array( 'type' => 'airforce', 'type_arg' => 3, 'nbr' => 3 ),
    array( 'type' => 'airforce', 'type_arg' => 4, 'nbr' => 2 ),
    array( 'type' => 'airforce', 'type_arg' => 5, 'nbr' => 1 ),
    array( 'type' => 'marines', 'type_arg' => 1, 'nbr' => 5 ),
    array( 'type' => 'marines', 'type_arg' => 2, 'nbr' => 4 ),
    array( 'type' => 'marines', 'type_arg' => 3, 'nbr' => 3 ),
    array( 'type' => 'marines', 'type_arg' => 4, 'nbr' => 2 ),
    array( 'type' => 'marines', 'type_arg' => 5, 'nbr' => 1 ),
    array( 'type' => 'special', 'type_arg' => 1, 'nbr' => 1 ),
    array( 'type' => 'special', 'type_arg' => 2, 'nbr' => 1 ),
    ...
    array( 'type' => 'special', 'type_arg' => 3, 'nbr' => 1 ),
    array( 'type' => 'special', 'type_arg' => 13, 'nbr' => 1 ),
    array( 'type' => 'special', 'type_arg' => 14, 'nbr' => 1 ),
    array( 'type' => 'special', 'type_arg' => 15, 'nbr' => 1 ),
    array( 'type' => 'africa', 'type_arg' => 1, 'nbr' => 1 ),
    array( 'type' => 'africa', 'type_arg' => 2, 'nbr' => 1 ),
    array( 'type' => 'africa', 'type_arg' => 3, 'nbr' => 1 ),
    array( 'type' => 'asia', 'type_arg' => 1, 'nbr' => 1 ),
    array( 'type' => 'asia', 'type_arg' => 2, 'nbr' => 1 ),
    array( 'type' => 'asia', 'type_arg' => 3, 'nbr' => 1 ),
    array( 'type' => 'asia', 'type_arg' => 4, 'nbr' => 1 ),
    array( 'type' => 'asia', 'type_arg' => 5, 'nbr' => 1 ),
    ....
   );
```

## Helper Functions

In addition to the base information about the cards, we'll need several helper functions that can tell us some info about a specific card. These are foundational to a lot of our game logic, and should therefore probably be tested pretty thoroughly.

- getCardName
- getCardHoverText
- getPathToCardImage
- isAttackCard
- isTerritoryCard
- for territory cards
    - territoryCountInContinent
- for special cards
    - resolution priority
