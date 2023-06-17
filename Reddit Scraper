import praw
import time
import pandas as pd
from datetime import datetime
import os
import pyautogui  # For visual notification
from playsound import playsound  # For audio notification
from dotenv import load_dotenv, find_dotenv

# Load .env file to get the OpenAI API key
load_dotenv(find_dotenv())

# Instantiate the Reddit API client
reddit = praw.Reddit(
    client_id=os.getenv('REDDIT_CLIENT_ID'),
    client_secret=os.getenv('REDDIT_CLIENT_SECRET'),
    user_agent="YOUR USER AGENT GOES HERE (by /u/YOUR REDDIT USERNAME GOES HERE)",
    check_for_async=False
)

# Load stock symbols and names
stock_data = pd.read_csv("ALL_STOCK_NAMES.csv")
symbols = stock_data['symbol'].tolist()
names = [name.lower() for name in stock_data['name'].tolist()]

def check_comments(user_name, phrases):
    user = reddit.redditor(user_name)
    for comment in user.comments.new(limit=50):
        for phrase in phrases:
            if phrase in comment.body:
                yield {
                    'date': datetime.fromtimestamp(comment.created_utc).isoformat(),
                    'headline': clean_comment(comment.body.split(phrase)[0].strip())
                }

def clean_comment(comment):
    # Removing unwanted sections and replacing newline characters with space
    cleaned_comment = comment.replace('^First ^Squawk ^[', '').replace('\n', ' ').strip()
    # Removing any remaining blank lines
    cleaned_comment = ' '.join(cleaned_comment.split())
    # Removing additional unwanted characters
    cleaned_comment = cleaned_comment.replace('^\\', '').replace('<', '').replace('>', '').replace('^', '')
    return cleaned_comment

def extract_ticker(comment):
    comment_words = comment.split()
    for word in comment_words:
        if word.upper() in symbols or word.lower() in names:
            return word.upper()
    return 'UNKNOWN'

def notify(latest_comment):
    # Play a sound for audio notification
    playsound('NewEntrySound.mp3')
    # Create a visual alert
    pyautogui.alert(f'Breaking News: {latest_comment}', 'Notification')

def is_comment_duplicate(comment, df):
    return df['headline'].str.contains(comment, regex=False).any()

def main():
    user_name = 'VisualMod'
    phrases = ['*Walter', 'FXHedge', '@FirstSquawk']
    filename = 'Breaking_News_Bloomberg_VM.csv'
    filename_2 = 'Breaking_News_Bloomberg_VM_timestamp_comment.csv'

    while True:
        new_comments = list(check_comments(user_name, phrases))

        # print new comments
        printable_comments = str(new_comments).encode('cp1252', errors='ignore').decode('cp1252')
        print("New comments: ", printable_comments)

        if new_comments:
            try:
                df = pd.read_csv(filename)
                df_2 = pd.read_csv(filename_2)
            except pd.errors.EmptyDataError:
                df = pd.DataFrame()
                df_2 = pd.DataFrame()

            for comment in new_comments:
                if not is_comment_duplicate(comment['headline'], df):
                    comment['stock'] = extract_ticker(comment['headline'])
                    df = pd.concat([pd.DataFrame([comment]), df], ignore_index=True)
                    df_2 = pd.concat([pd.DataFrame([{'date': comment['date'], 'headline': comment['headline']}]), df_2], ignore_index=True)
                    # Notify after writing to the CSV file
                    notify(comment['headline'])

            df = df[['stock', 'date', 'headline']]
            df_2 = df_2[['date', 'headline']]
            df.to_csv(filename, index=False)
            df_2.to_csv(filename_2, index=False)

            # print after writing to csv
            print("Data written to CSV.")
        else:
            print("No new comments found.")

        time.sleep(60)


if __name__ == "__main__":
    main()
