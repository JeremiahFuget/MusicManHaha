import os
import requests
from dotenv import load_dotenv

# Load environment variables from .env file
load_dotenv('token.env')

# Get the Spotify token from environment variable
spotify_token = os.getenv('SPOTIFY_TOKEN')

# Define a function to make requests to Spotify Web API
def fetch_spotify_api(endpoint, method='GET', body=None, params=None):
    headers = {
        "Authorization": f"Bearer {spotify_token}",
        "Content-Type": "application/json"
    }
    url = f"https://api.spotify.com/{endpoint}"

    response = requests.request(method, url, json=body, params=params, headers=headers)

    if response.status_code in [200, 201]:
        return response.json()
    else:
        print(f"Error: {response.status_code}, {response.text}")
        return None

# Fetch user's top tracks
def get_top_tracks():
    endpoint = "v1/me/top/tracks?time_range=long_term&limit=5"
    return fetch_spotify_api(endpoint)

# Get song recommendations based on top tracks
def get_recommendations(seed_tracks):
    endpoint = f"v1/recommendations?limit=5&seed_tracks={seed_tracks}"
    return fetch_spotify_api(endpoint)

# Get the current user's ID
def get_user_id():
    endpoint = "v1/me"
    user_info = fetch_spotify_api(endpoint)
    if user_info:
        return user_info['id']
    return None

# Create a playlist for the user
def create_playlist(user_id, playlist_name="My Recommended Playlist"):
    playlist_data = {
        "name": playlist_name,
        "description": "Playlist created based on top tracks and recommendations",
        "public": False
    }
    endpoint = f"v1/users/{user_id}/playlists"
    return fetch_spotify_api(endpoint, method='POST', body=playlist_data)

# Add tracks to a playlist
def add_tracks_to_playlist(playlist_id, track_uris):
    endpoint = f"v1/playlists/{playlist_id}/tracks"
    return fetch_spotify_api(endpoint, method='POST', params={"uris": track_uris})

# Main program to fetch top tracks, get recommendations, create a playlist and add tracks
if __name__ == "__main__":
    # Step 1: Fetch top tracks
    top_tracks = get_top_tracks()
    topTrackIDs = []

    if top_tracks:
        print("HERE ARE THE TOP 5 TRACKS...")
        for track in top_tracks['items']:
            name = track['name']
            track_id = track['id']
            topTrackIDs.append(track_id)
            artists = ', '.join(artist['name'] for artist in track['artists'])
            print(f"Name: {name} Artist: {artists} ID: {track_id} \n")

    # Step 2: Get recommendations based on top tracks
    seed_tracks = ','.join(topTrackIDs)  # Convert list to comma-separated string
    recommendations = get_recommendations(seed_tracks)

    recommended_track_uris = []
    if recommendations:
        print("Here are the recommendations based on the top tracks...")
        for track in recommendations['tracks']:
            name = track['name']
            track_uri = track['uri']  # Spotify URI for the track
            recommended_track_uris.append(track_uri)
            artists = ', '.join(artist['name'] for artist in track['artists'])
            print(f"Name: {name} Artist: {artists} URI: {track_uri}\n")

    # Step 3: Create a playlist
    user_id = get_user_id()
    if user_id:
        created_playlist = create_playlist(user_id)

        if created_playlist:
            playlist_id = created_playlist['id']
            print(f"Created playlist: {created_playlist['name']} (ID: {playlist_id})")

            # Step 4: Add tracks to the playlist
            all_track_uris = [f"spotify:track:{track_id}" for track_id in topTrackIDs] + recommended_track_uris
            add_tracks_response = add_tracks_to_playlist(playlist_id, all_track_uris)

            if add_tracks_response:
                print(f"Tracks successfully added to playlist: {created_playlist['name']}")
                for track in created_playlist['tracks']:
                    print(f"Name: {name} Artist: {artists}")
            else:
                print("Failed to add tracks to the playlist.")
        else:
            print("Failed to create playlist.")
    else:
        print("Failed to retrieve user ID.")

#This was done with the assistance of Kyah. H, ChatGPT, Postman Application, Experimental AI. 
