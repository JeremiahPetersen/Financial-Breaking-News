This script uses the Reddit API to check for financial breaking news from a reddit bot. New news is displayed for the user in a small pop up.

You will need to add your REDDIT_CLIENT_ID, REDDIT_CLIENT_SECRET, and OPENAI_API_KEY in the .env file

This is a work in progress and currently only displays the breaking news, and saves it to a csv file.  (appends new data to the same file, with the newest data being at the top)

Eventually this project will also correctly identify which stock the headline is referring too, and run the stock+headline through a sentiment analysis using ChatGPT

This project will be updated if Reddit decides to charge too much for their API use.

The Twitter version checks 3 twitter accounts that the reddit bot was also checking.
