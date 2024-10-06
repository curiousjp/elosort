# `elosort`
## A simple python tool to sort files by Elo Rating

`elosort` lets you sort your photos by quality, based on 'a or b' preference questions.

### Notes from the forker:
This fork has slowly diverged away from @robertksmiles' original work to better suit my needs. At a high level, some of these changes are:
* compatibility with Python3 and codebase adjustments to match what I think current best practice is 
* output styled with a built-in stylesheet
* dependency on sqlite3 is gone, with data now backed by `shelve`
* an endpoint, `/script`, provides a pasteable set of Unix commands to remove all but the top-*n* rated items (by default, 20). You can click on the script block to select it for copying and pasting
* many changes to the underlying 'depletable stack' of matches that are provided for review - these are aimed at focusing attention in on the top ranked items:
  * the size of the depletable stack can either be capped (see the command line argument `--session`) or shrink over time (`--poolshrink`). If `poolshrink` is greater than 0, the number of matches will be capped at `math.ceil(len(items) * (1 - (iteration / (iteration + poolshrink))))` where iteration is the number of times the stack has been depleted on this run.

  * items are grouped into four equally spaced Elo bands, and matchups are selected randomly from within the band. Any leftover items are pushed to the next band - anything pushed out of the last band is randomly matched with another item from any band.

  * instead of searching the disk for files, pre-made shelf files can be provided (e.g. from a clustering algorithm or a tool like DiffusionToolkit) using `--prefilled` and the `--dbname` arguments. Shelf structure should be a single list of tuples, (filename, rating).

* The result view (and rating page) now supports some forms of video. You may wish to constrain the result view page (`&count=50` etc) when working with video as there is _no lazy loading_ and it is now unpaginated by default. The result page allows for the selection of two items, which can be switched or forced into a match using the links at the top of the page. 

* On the rating screen, in addition to left/a, right/d to select the round winner, up/a will award a tie, and down/s will take you to the results page. The delete key invokes a special round state that causes *both items* to record a loss. The rating screen will not consume items from the depletable stack until they are voted on.

I have unfortunately introduced a number of linuxisms to this fork - references to `/dev/stdout` and `/dev/null`, use of `rm` in the script, removal of `\r\n` linebreaks, etc. I feel a little bad about this, but suggest running the script inside of WSL if this is a problem for your environment.

Original README follows:

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
