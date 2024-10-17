# MusicManHaha
This is just a stab at a Spotify api. Starting off with something simple, but plan to do something Mario kart themed. 

#This is the initial code for connecting with the token. 
import os
import requests
from dotenv import load_dotenv

# Load environment variables from .env file. / Thanks Carson M. for assistance and explanation. 
load_dotenv('token.env')
print(load_dotenv('token.env'))
print()

# Get the Spotify token from environment variable
spotify_token = os.getenv('SPOTIFY_TOKEN')

This is the referenced spotify token. Ran into many issues with it not running, primarily because I didn't save...lol
SPOTIFY_TOKEN="BQAepYme50817QxSDo7zMmvTVASe9LX3ir_b3vHT1QBg92Wh3r42ILRx2F8BAZhEFR-4NkNN9r7yJTQ8lHl9YiPXSgVJmZqVS_qNBwu7H4zYyzhFdW648RUWyUyQM-5yUfYUdseZrHFoU2IL2DdelgyaSQNtqmD5lzRFQZ-nwVb55k9aeB2MAW_GFfgRatLT6FMSFOd--rr1xATT8QLNOu0FVkK3g_oaJa-hr6th4cTf8DzZBCLU27qBv3TpXQRLUu2YRAd1f4Nfcd_ENwvhXjZJGtgOLHlp"
