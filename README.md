import os
import requests
from dotenv import load_dotenv

# Load environment variables from .env file. / Thanks Carson M. for assistance and explanation. 
load_dotenv('token.env')
print(load_dotenv('token.env'))
print()

# Get the Spotify token from environment variable. This one gave me the most trouble because I wasn't saving...lol
spotify_token = os.getenv('SPOTIFY_TOKEN')

# Define a function to make requests to Spotify Web API
def fetch_spotify_api(endpoint, method='GET', body=None):
    headers = {
        "Authorization": f"Bearer {spotify_token}",
        "Content-Type": "application/json"
    }
    url = f"https://api.spotify.com/{endpoint}"

    response = requests.request(method, url, json=body, headers=headers)

    if response.status_code == 200:
        return response.json()
    else:
        print(f"Error: {response.status_code}, {response.text}")
        return None

# Fetch user's top tracks
def get_top_tracks():
    endpoint = "v1/me/top/tracks?time_range=long_term&limit=5"
    return fetch_spotify_api(endpoint)

topTrackIDs = []
#Print Song Recommendations. 
def get_recommendations():
    endpoint = f"v1/recommendations?limit=5&seed_tracks={newIDs}"
    return fetch_spotify_api(endpoint)
    

# Print top tracks
if __name__ == "__main__":
    top_tracks = get_top_tracks()
    
    if top_tracks:
        print("HERE ARE THE TOP 5...")
        for track in top_tracks['items']:
            name = track['name']
            id = track['id']
            topTrackIDs.append(str(id))
            artists = ', '.join(artist['name'] for artist in track['artists'])
            print(f"Name: {name} Artist: {artists} ID: {id} \n") 
     
    newIDs = ','.join(topTrackIDs)
    recommendations = get_recommendations()
    if recommendations:
        print("Here are the recommendations based on said songs...") #Printing out the recommendations based on the previous top listed ones. 
        for track in recommendations['tracks']:
            name = track['name']
            artists = ','.join(artist['name'] for artist in track['artists'])
            print(f" Name: {name} Artist: {artists}\n")



