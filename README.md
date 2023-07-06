# NMC.Vision API docs

## Introduction
API endpoints are used to retrieve single assets, assets name values and taxonomy values.
A websocket is used to search assets.

## General info
Base URLs
- API: https://api.nmc.vision/api
- Websocket: wss://ws.nmc.vision

## Useful endpoints
### Retrieving single assets
#### Asset by hex id
`GET /asset/hex/{hex}`
#### Asset by name
Getting assets by name is not the recommended way.
We have some logic to find the best match, that defaults to the first asset found.
However it is not perfect and could return the wrong asset. <br />
`POST /asset/find`
```json
{
    "name": "fuckyea"
}
```

#### Asset name values
`GET /asset/hex/{hex}/name_values`

---

### Searching for assets
1. Establish a connection to the websocket server at `wss://ws.nmc.vision`
2. Upon successful connection, you can start sending search requests.
3. Send a JSON message with the following structure:
   - `type`: Specifies the type of message, which should be set to `'search'`
   - `search`: Contains the search parameters and options for asset searching

```javascript
// Example
webSocket.send(JSON.stringify({ type: 'search', search: { name: 'fuckyea' } }))
```

4. Search options:
```javascript
/**
 * @typedef {object} FindAssetsOptions
 * @property {string} [trait] - Trait slug
 * @property {string} [traits] - Comma separated list of trait slugs
 * @property {string} [sort_by] - id, name, creation, expiration, main_data
 * @property {import("mongodb").SortDirection} [dir]
 * @property {(string|number)} [page]
 * @property {(string|number)} [limit] - Max 250
 * @property {string} [search] - Fuzzy search
 * @property {string} [date_start] - yyyy-mm-dd
 * @property {string} [date_end] - yyyy-mm-dd
 * @property {boolean} [exact_hex] - This property is here for utf8 name search for asset detail url
 * @property {(string|boolean)} [expired]
 * @property {boolean} [hasImage]
 * @property {string} [owner]
 * @property {string} [requestId] - Used to manage race conditions
 */
```

1. The websocket server will process the search request and return the results by batches until the limit is reached or no more result is found
2. The server sends three types of messages back to the client:
   - `assets`: Contains the matching assets based on the search criteria
   - `count`: Provides the total count of assets found
   - `done`: Indicates that the search is complete
3. You can listen to these messages and handle them accordingly in your application
4. To perform another search, send another search request message

### Retrieving traits
`GET /traits/`