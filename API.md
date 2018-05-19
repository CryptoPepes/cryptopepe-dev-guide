# CryptoPepe API documentation

The API is there to speed up the front-end, 
and enable users to search through all the existing CryptoPepes very fast.
This is NOT to replace the smart contracts; the API is purely for better accessibility and speed.
 The same pepe data can be retrieved directly from the blockchain (E.g. using Web3).
The API also offers an end-point to retrieve SVG images for each Pepe.
At release, the rule-set used to convert pepe DNA to images will be publicly available,
 so that everyone can verify the API responses.

## End-points

```
/api/getPepe/:pepeId
/api/getPepeSVG/:pepeId
/api/search
/api/getUser/:address
```

### `/api/getPepe/:pepeId`

```
/api/getPepe/:pepeId

Path params:
------------
pepeId    -- Decimal base, the Id of the pepe

Query params:
-----------
None.

Response:
-----------
Note: the `color` type is a string, 6-character RGB hex code, prefixed with "#".

{
  "lcb": int,                          -- Block number since last update
  "name": string,                      -- Name of the pepe, "" if no name
  "cool_down_index": int,              -- Cooldown stage, represented by index. (Corresponding to index in the smartcontract)
  "can_cozy_again": int (timestamp),   -- Timestamp for the time when the pepe can have a cozy time again, 0 if directly
  "gen": int,                          -- The generation of the Pepe
  "father": string,                    -- Integer number formatted as string (Big number support), the pepeId of the father
  "mother": string,                    -- Similar to "father", but for the mother.
  "pepeId": string                     -- Big number, the pepeId of the pepe itself.
  "master": string (ethereum address),
  "birth_time": int (timestamp),       -- Timesamp of the birth
  "look": {                            -- The look of the pepe, result of parsing the DNA, see look documentation
    "body": {
      "shirt": {
        "type": string,
        "color": color,
      },
      "neck": string,
    },
    "skin": {
      "color": color,
    },
    "background": color,
    "extra": {
      "glasses": {
        "type": string,
        "primary": color,
        "secondary": color
      }
    },
    "head": {
      "eyes": {
        "type": string,
        "color": color
      },
      "mouth": string,
      "hair": {
        "type": string,
        "haircolor": color,
        "hatcolor2": color,
        "hatcolor": color
      }
    }
  },
  "genotype": "[[[111 112 113 114] [121 122 123 124]] [[211 212 213 214] [221 222 223 224]]]" -- Each of the numbers is a 32 bit integer, 2 * 2 * 4 * 32 = 512 bits of DNA
}

```

### `/api/getPepeSVG/:pepeId`

```
/api/getPepeSVG/:pepeId

Path params:
------------
pepeId    -- Decimal base, the Id of the pepe

Query params:
-----------
None.

Response:
-----------
The response body is an SVG image of the requested Pepe.
```

### `/api/search`

```
/api/search

Path params:
------------
None.

Query params:
-----------
Note: every parameter is optional.

cursor          string       -- Encrypted pointer to the database index position to continue from, for infinite scrolling.
limit           int          -- Amount of pepes to retrieve. Limited to 20, defaults to 20.
listingType     string       -- The type of listing. defaults to all. "cozy" = hopping/cozy auction. "sale" = pepe auction.
owner           string       -- The owner address of the pepe. (If listingType is "cozy" or "sale", then this overwrites the cozySeller or saleSeller parameters)
cozySeller      string       -- The seller address of the pepe that is being sold.
saleSeller      string       -- The seller address of the pepe that is offered for cozy time.
cooldownIndex   int          -- The cooldown stage.
props           string       -- List of pepe-properties to filter for.
sort            string       -- Sorting order.
displayName     string       -- The name of the pepe. Leave out the left "\u0000" padding. 32 bytes max length.
mother          int          -- Pepe-id of the mother. Must be decimal base.
father          int          -- Pepe-id of the father, formatted like the mother pepe-id.
gen             int          -- Filter for a particular generation of pepes.

Param Details:
----------------------

props:
    A prop is prefixed by it category, e.g. shirt>basic_shirt
    
Categories:
    eyes
    hair
    mouth
    shirt
    neck
    glasses

The second part is the id of the part.

Parts are added almost every week as of now, this part of the documentation is still a work in progress.


Sorting orders:
    newest-first
    oldest-first
    high-pepe-begin-price-first
    low-pepe-begin-price-first
    high-pepe-end-price-first
    low-pepe-end-price-first
    high-cozy-begin-price-first
    low-cozy-begin-price-first
    high-cozy-end-price-first
    low-cozy-end-price-first
    fast-cozy-again-first
    slow-cozy-again-first

Response:
-----------

{
  "pepes": [                    -- a list of pepes, each is formatted like the getPepe endpoint returns.
    ...pepes
  ],
  "hasMore": boolean,           -- If the query has more results, after response the limit
  "cursor": string              -- Cursor, use this with the other parameters exactly the same, and get the next page of pepes. 
}


```

### `/api/getUser/:address`

```
/api/getUser/:address

Path params:
------------
address    -- Ethereum address, including the "0x" prefix.

Query params:
-----------
None.

Response:
-----------

{
  "lcb": int,                          -- Block number since last update
  "username": string                   -- Name of the user, "" if not set.
}

```
