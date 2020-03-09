# Gimme food
[![Build Status](https://travis-ci.com/matrulda/gimme-food.svg?branch=master)](https://travis-ci.com/matrulda/gimme-food)
[![codecov](https://codecov.io/gh/matrulda/gimme-food/branch/master/graph/badge.svg)](https://codecov.io/gh/matrulda/gimme-food)


`Gimme food` is an application to easily make a shopping list from your favorite recipes when it's time for the weekly shopping or "storhandling" as we say in Swedish. The recipes are chosen randomly, but you are able to tweak the list of chosen recipes by specifying wanted ingredients.

## Installation
```
git clone git@github.com:matrulda/gimme-food.git
cd gimme-food
pip install -r requirements/prod .
```
## Recipe database
The recipe database is a folder with json files, with one recipe in each.
Specify this folder in the config, see `gimme_food/config/config.yaml`. Data structure
of recipes are described below, for more examples see `gimme_food/examples`.

### Defining your own recipes
Recipes are written in json format, like this:
```
{
  "recipe": {
    "name": "Awesome recipe",
    "url": "https://awesomerecipes.com/awesome-recipe",
    "portions": 4,
    "ingredients": {
      "Awesome ingredient": {"amount": 0.5, "amount_type": "liter"},
      "Another awesome ingredient": {"amount": 2, "amount_type": "piece"}
      }
    }
}
```

There are 5 amount types:
  - **liter**, example: `0.5 liter milk`
  - **gram**, example: `25 gram ginger`
  - **piece**, example: `3 pieces potato`
  - **portion**, example: `4 portions rice`
  - **unknown**, this amount type is a way to add an ingredient without specifying how much. An example is `salt`

Important:
  - Remember to always write your ingredients in the same format. The app is not very smart and will treat `carrots`, `Carrot` and `carrot` as different things.
  - Don't mix amount types for an ingredient. For example, if one recipe contain `500 gram potato` and another `3 pieces potato` it won't be possible to sum them.  

## GIMME RECIPE!
Does it sound dull to write your own json files? Fear not, `gimme_recipe` here for the rescue! It is now possible to use the `gimme_recipe` command to add https://www.ica.se/recept/ recipes to your collection. Read more about this awesome feature in the usage section.

## Usage: `gimme_food`
```
$ gimme_food -n 2

------------------------------------------------------------------
  ________.__                           _____                 .___
 /  _____/|__| _____   _____   ____   _/ ____\____   ____   __| _/
/   \  ___|  |/     \ /     \_/ __ \  \   __\/  _ \ /  _ \ / __ |
\    \_\  \  |  Y Y  \  Y Y  \  ___/   |  | (  <_> |  <_> ) /_/ |
 \______  /__|__|_|  /__|_|  /\___  >  |__|  \____/ \____/\____ |
        \/         \/      \/     \/                           \/
-------------------------------------------------------------------
                              v1.0.0

The following recipes were chosen:

Linscurry med kokosmjölk och lime: https://www.ica.se/recept/linscurry-med-kokosmjolk-och-lime-720274/

Fruktig kokosmjölksgryta: https://undertian.com/recept/fruktig-kokosmjolksgryta/


Here's your shopping list:
4 portioner Ris
2 dl Torkade röda linser
3 Gul lök
1 msk Färsk ingefära
1 tsk Sambal oelek
1 msk Olja
4 Vitlöksklyfta
37 krm Curry
2 tsk Tomatpuré
5 dl Grönsaksbuljong
1.2 liter Kokosmjölk
1/2 Blomkålshuvud
250 gram Körsbärstomater
1 Lime
Salt
Peppar
65 gram Babyspenat
1 dl Cashewnötter
1 liter Kokade kikärtor
1/2 dl Jordnötssmör
400 gram Krossade tomater
3 Banan
225 gram Frusen mango
1 Grönsaksbuljongtärning

```
The `-n/--number-of-recipes` parameter specifies how many recipes you want. In this example the default config is used which will use the two example recipes. A diet of just these recipes is not recommended.   

Use the `-c/--config` parameter to use your own config that points to your own recipe folder.   

The ingredient parameter (`-i/--ingredient`) can be used to tweak your list of chosen recipes. Maybe there's a certain ingredient you crave for the moment or perhaps there is an ingredient you have at home and want to use. This parameter can be specified several times, like this:
```
$ gimme_food -n 1 -i "Banan" -i "Curry"

```
The app will try to pick one recipe that contain as many of your wanted ingredients as possible. After doing this is will try to find a recipe that best match the remaining ingredients, and so on. When all wanted ingredients have been found, random recipes will be added until the list contains the specified number of recipes. In short, the algorithm tries to find your ingredients in as few recipes as possible. This means that, this command:
```
$ gimme_food -n 5 -i "Banan" -i "Curry"

```
 will aim to find one recipe with `Banan` and `Curry` + 4 random recipes. It will NOT try to find 5 recipes with those ingredients. If there is no recipe containing `Banan` + `Curry`, it will try to find one with `Banan` and another with `Curry` + 3 random recipes.

 ## Usage: `gimme_recipe`

 ```
 $ gimme_recipe -c my_gimme_food_conf.yaml --url https://www.ica.se/recept/rotfruktscurry-med-nudlar-och-koriander-723758/

------------------------------------------------------------------
  ________.__                           _____                 .___
 /  _____/|__| _____   _____   ____   _/ ____\____   ____   __| _/
/   \  ___|  |/     \ /     \_/ __ \  \   __\/  _ \ /  _ \ / __ |
\    \_\  \  |  Y Y  \  Y Y  \  ___/   |  | (  <_> |  <_> ) /_/ |
 \______  /__|__|_|  /__|_|  /\___  >  |__|  \____/ \____/\____ |
        \/         \/      \/     \/                           \/
-------------------------------------------------------------------
                              v1.1.0


Ingredient name, Grönsaksbuljongtärning eller motsvarande mängd fond, is suspiciously long, perhaps it contains more than the ingredient name.
Do you want to use it? Type "yes".
Otherwise, type the name you want to use instead: Grönsaksbuljongtärning

Should, Port glasnudlar, be interpreted as as portions of glasnudlar? (yes/no):
yes

Recipe captured! Writing to: /json_recipes/rotfruktscurry_med_nudlar_och_koriander.json
 ```

 To use the `gimme_food` feature, you have to sign up on https://www.ica.se/ansokan/ and add your username and password to the config. See example in `gimme_food/config/config.yaml`.

 Use the `-u/--url` parameter to specify the recipe url.

 Too tackle the problem of recipes often being written in a non-standardized way, the algorithm will try to figure out what you actually want to add to your shopping list and sometimes ask you for help.
  * Ingredient names with 3 or more words will be marked as suspiciously long. You can choose to keep the long name or state another name to use.
  * Ingredient names that start with "Port" will be marked as portion ingredient. You can state whether or not this is correct.
  * "Salt och peppar" will be separated into two ingredients: `Salt` and `Peppar`

The algorithm is not perfect. If you discover that something was interpreted incorrect, please update the json file manually. Submit a ticket if it is a case a machine should be able to handle.   

## Note regarding measures
Since this is an app to help me in my everyday life and I mainly use Swedish recipes, amounts will be converted to appropriate Swedish measures. In later versions different language options might be possible.
