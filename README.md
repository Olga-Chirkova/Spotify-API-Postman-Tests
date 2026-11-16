# Spotify Web API: Postman tests

A Postman collection I built to practice API test automation with a security angle. I come from QA, and I'm moving into application security. I wanted a project where both sides show up in the same place: regular functional checks plus tests for what happens when requests are wrong, unauthenticated, or hostile.

I picked the Spotify Web API because it uses real OAuth 2.0 with scopes, has a full resource lifecycle (playlists), and is free to test against.

> Independent learning project. Not affiliated with or endorsed by Spotify.

## What's in the collection

**Playlist lifecycle (happy path).** Get the current user, create a private playlist, update its metadata, add a track, read it back, remove the track, confirm it's gone. Requests are chained: user ID and playlist ID are saved to environment variables and reused. [N] requests, [N] assertions.

**Negative and security checks.**
- Protected endpoint with no token
- Invalid bearer token
- Malformed track URI and invalid playlist ID
- Request body with missing required fields
- Access to a playlist owned by a second test account (`FOREIGN_PLAYLIST_ID`)

Assertions cover status code, response body shape, data types, and that error responses don't leak anything they shouldn't.

## Observations





## Running it

1. Import `collections/Spotify_SDET_AppSec.postman_collection.json` and `environments/Spotify.postman_environment.json` into Postman.
2. Create an app in the Spotify developer dashboard and get an OAuth token with these scopes: `user-read-private`, `playlist-read-private`, `playlist-modify-private`.
3. Put the token in `ACCESS_TOKEN` and a valid track URI in `TRACK_URI`.
4. Run the collection in order.

Notes:
- The invalid-token requests override the collection-level auth on purpose, so they don't inherit the good token.
- The cleanup request is separate. Check the playlist ID before running it.
- Tokens are never committed. Use a secret-type variable in Postman.
- I used dedicated test accounts, not my personal one.

## Environment variables

`BASE_URL`, `ACCESS_TOKEN`, `INVALID_ACCESS_TOKEN`, `LIMITED_SCOPE_TOKEN`, `USER_ID`, `PLAYLIST_ID`, `FOREIGN_PLAYLIST_ID`, `TRACK_URI`, `RUN_ID` (unique per run, used in playlist names).

## Next

1. Newman run from the command line, with an HTML report in `reports/`
2. GitHub Actions to run it on every push
3. JSON schema validation for responses
4. Tests with a limited-scope token (expecting 403)

Further down the list: boundary values and more access-control cases.

