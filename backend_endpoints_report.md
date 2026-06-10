# Backend Endpoints Report

This report documents the backend services, third-party APIs, and their respective endpoints found in the codebase under the `core/service/` directory.

## 1. YouTube Music Service (`kotlinYtmusicScraper`)

This service interacts primarily with the YouTube Music internal API, and some third-party extensions.

### Core YouTube Music API Endpoints
Base URL: `https://music.youtube.com/youtubei/v1/`

*   **Search**
    *   **Method:** POST
    *   **Endpoint:** `search`
    *   **Body (`SearchBody`):**
        *   `context` (Context): Client metadata (clientName, version, locale, visitorData)
        *   `query` (String?): The search query.
        *   `params` (String?): Additional search parameters.
    *   **Query Parameters:** `continuation`, `ctoken`

*   **Player / Init Playback**
    *   **Method:** POST
    *   **Endpoint:** `player`
    *   **Body (`PlayerBody`):**
        *   `context` (Context): Client metadata.
        *   `videoId` (String): The video ID to play.
        *   `playlistId` (String?): The playlist ID.
        *   `cpn` (String?): Client Playback Nonce.
        *   `contentCheckOk` (Boolean, default true): Content check flag.
        *   `racyCheckOk` (Boolean, default true): Racy check flag.
        *   `playbackContext` (PlaybackContext): Contains `signatureTimestamp`.
        *   `serviceIntegrityDimensions` (ServiceIntegrityDimensions): Contains `poToken`.

*   **Browse / Playlist Details**
    *   **Method:** POST
    *   **Endpoint:** `browse`
    *   **Body (`BrowseBody`):**
        *   `context` (Context): Client metadata.
        *   `browseId` (String?): The browse ID (e.g., playlist ID).
        *   `params` (String?): Browse parameters.
        *   `formData` (FormData): Selected values for location/region.
        *   `continuation` (String?): Continuation token.
    *   **Query Parameters:** `alt`

*   **Next (Up Next / Autoplay)**
    *   **Method:** POST
    *   **Endpoint:** `next`
    *   **Body (`NextBody`):**
        *   `context` (Context): Client metadata.
        *   `videoId` (String?): Current video ID.
        *   `playlistId` (String?): Current playlist ID.
        *   `playlistSetVideoId` (String?): Specific video set ID in the playlist.
        *   `index` (Int?): Current index in the playlist.
        *   `params` (String?): Next parameters.
        *   `continuation` (String?): Continuation token.

*   **Get Search Suggestions**
    *   **Method:** POST
    *   **Endpoint:** `music/get_search_suggestions`
    *   **Body (`GetSearchSuggestionsBody`):**
        *   `context` (Context): Client metadata.
        *   `input` (String): The search input string.

*   **Get Queue**
    *   **Method:** POST
    *   **Endpoint:** `music/get_queue`
    *   **Body (`GetQueueBody`):**
        *   `context` (Context): Client metadata.
        *   `videoIds` (List<String>?): List of video IDs.
        *   `playlistId` (String?): Playlist ID.

*   **Account Menu**
    *   **Method:** POST
    *   **Endpoint:** `account/account_menu`
    *   **Body (`AccountMenuBody`):**
        *   `context` (Context): Client metadata.
        *   `deviceTheme` (String): Device theme setting.
        *   `userInterfaceTheme` (String): UI theme setting.

*   **Playlist Actions**
    *   **Create Playlist:**
        *   **Method:** POST
        *   **Endpoint:** `playlist/create`
        *   **Body (`CreatePlaylistBody`):** `context`, `title`, `description`, `privacyStatus`, `videoIds`
    *   **Edit Playlist (Rename, Add, Remove, Move Items):**
        *   **Method:** POST
        *   **Endpoint:** `browse/edit_playlist`
        *   **Body (`EditPlaylistBody`):** `context`, `playlistId`, `actions` (List of Action with `action`, `playlistName`, `addedVideoId`, `removedVideoId`, `setVideoId`, `movedSetVideoIdSuccessor`)

*   **Like / Unlike**
    *   **Like:** `POST like/like`
    *   **Unlike:** `POST like/removelike`
    *   **Body (`LikeBody`):** `context`, `target` (with `videoId`)

### Third-Party & Related Endpoints (in YouTube Scraper)

*   **Return YouTube Dislike API**
    *   **Method:** GET
    *   **URL:** `https://returnyoutubedislikeapi.com/Votes`
    *   **Query Parameter:** `videoId`

*   **SponsorBlock API**
    *   **Method:** GET
    *   **URL:** `https://sponsor.ajay.app/api/skipSegments/`
    *   **Query Parameters:** `videoID`, `category` (multiple), `service=YouTube`

*   **SimpMusic Charts API**
    *   **Method:** GET
    *   **URL:** `https://chart.simpmusic.org/api/playlists`

*   **Piped Instances (Streams)**
    *   **Method:** GET
    *   **URL:** `{pipedInstance}/streams/{videoId}`

*   **Google Suggest Queries**
    *   **Method:** GET
    *   **URL:** `http://suggestqueries.google.com/complete/search`
    *   **Query Parameters:** `client=firefox`, `ds=yt`, `q={query}`

*   **Tidal Search / Stream**
    *   **Search URL:** `{tidal_url}/search` (GET, param `s`)
    *   **Track URL:** `{tidal_url}/track` (GET, params `id`, `quality`)
    *   **Uptime URL:** `https://tidal-uptime.jiffy-puffs-1j.workers.dev/`

*   **PO Token Generation (Google Internal)**
    *   **Method:** POST
    *   **Create URL:** `https://jnn-pa.googleapis.com/$rpc/google.internal.waa.v1.Waa/Create`
    *   **Generate URL:** `https://jnn-pa.googleapis.com/$rpc/google.internal.waa.v1.Waa/GenerateIT`
    *   **Headers:** Requires specific headers like `x-goog-api-key`.


## 2. Lyrics Service (`lyricsService`)

Provides custom lyrics API along with integrations to external lyrics providers.
Base URL: `https://api-lyrics.simpmusic.org/v1/`

*   **Get Lyrics by Video ID**
    *   **Method:** GET
    *   **Endpoint:** `{videoId}`
    *   **Headers:** `X-Timestamp`, `X-HMAC`

*   **Get Translated Lyrics**
    *   **Method:** GET
    *   **Endpoint:** `translated/{videoId}/{language}`

*   **Insert Lyrics**
    *   **Method:** POST
    *   **Endpoint:** `/`
    *   **Body (`LyricsBody`):** `videoId`, `songTitle`, `artistName`, `albumName`, `durationSeconds`, `plainLyric`, `syncedLyrics`, `richSyncLyrics`, `trackType`, `contributor`, `contributorEmail`

*   **Insert Translated Lyrics**
    *   **Method:** POST
    *   **Endpoint:** `translated`
    *   **Body (`TranslatedLyricsBody`):** `videoId`, `translatedLyric`, `language`, `contributor`, `contributorEmail`

*   **Vote Lyrics**
    *   **Method:** POST
    *   **Endpoint:** `vote`
    *   **Body (`VoteBody`):** `id`, `vote` (1 for upvote, 0 for downvote)

*   **Vote Translated Lyrics**
    *   **Method:** POST
    *   **Endpoint:** `translated/vote`
    *   **Body (`VoteBody`):** `id`, `vote`

*   **External: Lrclib Lyrics Search**
    *   **Method:** GET
    *   **URL:** `https://lrclib.net/api/search`
    *   **Query Parameters:** `q` ({artist} {track})

*   **External: BetterLyrics Search**
    *   **Method:** GET
    *   **URL:** `https://lyrics-api.boidu.dev/getLyrics`
    *   **Query Parameters:** `s` (track), `a` (artist), `d` (duration)


## 3. Spotify Service (`spotify`)

Interacts with various Spotify endpoints, mainly to retrieve metadata and lyrics to augment the app's data.

*   **Get Server Time**
    *   **Method:** GET
    *   **URL:** `https://open.spotify.com/api/server-time`
    *   **Headers:** `Cookie` (sp_dc)

*   **Get Access Token**
    *   **Method:** GET
    *   **URL:** `https://open.spotify.com/api/token`
    *   **Query Parameters:** `reason`, `productType`, `totp`, `totpServer`, `totpVer`

*   **Get Lyrics Token**
    *   **Method:** GET
    *   **URL:** `https://open.spotify.com/get_access_token?reason=transport&productType=web_player`

*   **Get Spotify Lyrics**
    *   **Method:** GET
    *   **URL:** `https://spclient.wg.spotify.com/color-lyrics/v2/track/{trackId}?format=json&vocalRemoval=false&market=from_token`
    *   **Headers:** `Authorization`, `Client-Token`

*   **Search Spotify Track (Pathfinder GraphQL)**
    *   **Method:** GET
    *   **URL:** `https://api-partner.spotify.com/pathfinder/v1/query?operationName=searchTracks`
    *   **Query Parameters:** `variables` (JSON string with searchTerm, limit, etc.), `extensions` (JSON string with sha256Hash)

*   **Get Spotify Canvas**
    *   **Method:** POST
    *   **URL:** `https://spclient.wg.spotify.com/canvaz-cache/v0/canvases`
    *   **Body (`CanvasBody`):** `tracks` (List of Track with `track_uri`)

*   **Get Client Token**
    *   **Method:** POST
    *   **URL:** `https://clienttoken.spotify.com/v1/clienttoken`
    *   **Body (`SpotifyClientBody`):** `client_data` (containing client_version, client_id, and js_sdk_data)

*   **Spotify TOTP Secrets (Third-party repo)**
    *   **Method:** GET
    *   **URL:** `https://raw.githubusercontent.com/xyloflake/spot-secrets-go/refs/heads/main/secrets/secretDict.json`


## 4. AI Service (`aiService`)

Provides lyrics translation capabilities using AI providers.

*   **OpenAI / Custom OpenAI / Gemini**
    *   **Method:** POST (via OpenAI Client SDK)
    *   **Endpoints:** Uses `chatCompletionRequest` internally to reach `https://api.openai.com/v1/chat/completions` or Gemini equivalent.
    *   **Request Details:** It sends the lyrics mapped to indices as a JSON string, and asks the AI to return a specific JSON Schema (translating the lines).


## 5. Kizzy Service / Discord RPC (`kizzy`)

Integrates with Discord Rich Presence and Kizzy APIs.

*   **Kizzy Upload Image**
    *   **Method:** POST
    *   **URL:** `https://kizzy-api.cjjdxhdjd.workers.dev/upload`
    *   **Body:** `MultiPartFormDataContent` with the image bytes.

*   **Kizzy Get Image**
    *   **Method:** GET
    *   **URL:** `https://kizzy-api.cjjdxhdjd.workers.dev/image`
    *   **Query Parameter:** `url`

*   **Discord User Info**
    *   **Method:** GET
    *   **URL:** `https://discord.com/api/v9/users/@me`
    *   **Headers:** `Authorization` (token)

*   **Discord Gateway (WebSocket)**
    *   Handles presence updates, heartbeat, and connection with the Discord Gateway to display current playing status.
