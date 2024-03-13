# `elosort`
## A simple python tool to sort files by Elo Rating

`elosort` lets you sort your photos by quality, based on 'a or b' preference questions.

### Notes from the forker:
This fork has some minor modifications to bring it up to compatibility with Python3, make some small improvements in line with what I think current best practice is, and to add a few features I find personally useful. The most significant of these are:
* Output has been styled with a built-in stylesheet. 
* An endpoint, `/script`, has been created to provide you with a pasteable set of Unix commands to remove all but the top-*n* rated images (by default, 20). You can click on the script block to select it for copying and pasting.
* The stack is now rebalanced with a randomly chosen item when it is reshuffled or when a peek is attempted with insufficient items remaining (possible if an item somehow ends up being matched against itself on a degenerately small stack).
* On the main rating page, the up arrow key will now register a draw, and the down arrow key will take you to the results screen. Viewing the index no longer "consumes" items of the stack to be rated, this only happens when a result is submitted.
* The results screen, by default, is no longer paginated and will dump all the images in the dataset to your screen at once.

I have unfortunately introduced a number of linuxisms to this fork - references to `/dev/stdout` and `/dev/null`, use of `rm` in the script, removal of `\r\n` linebreaks, etc. I feel a little bad about this, but suggest running the script inside of WSL if this is a problem for your environment.

## The idea

The [Elo rating system](http://en.wikipedia.org/wiki/Elo_rating_system) was developed to rate chess players. The players' scores are adjusted with each game, depending on both the outcome of the game and the relative ratings of the players.

`elosort` applies this algorithm to a set of files (only image files are currently supported) on your hard drive.

## Prerequisites

- Python (written for version 3.1)
- [CherryPy](https://docs.cherrypy.dev/en/latest/)

## Usage

Run `elosort` in the directory with the photos, which will be shuffled, and then point your browser at `http://localhost:8080`. Click the image you prefer (or press the left or right arrow key). The vote is registered, and a new pair of images will appear. Once you have rated each pair of images in your dataset, they will be shuffled and rating will continued. (A message is printed to the console to show this.) After you've done some voting, you can look at the rankings at the `http://localhost:8080/results` page.

Run `elosort --help` to get information about command-line options.

## Notes

This is pre-alpha software, i.e. unfinished and unfit for normal use. Expect problems, and please be sure to report any bugs you find.

It's also appallingly inefficient software right now, particularly in the hashing and database handling, but this is actually all a cunning ruse: The plan is that someone will read the source and be unable to resist correcting it, thus giving me an opportunity to learn about collaborating with strangers over GitHub.