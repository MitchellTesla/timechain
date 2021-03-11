Bitnodes is currently being developed to estimate the size of the Bitcoin network by finding all the reachable nodes in the network.

BITNODES API V1.0
All API endpoints are available for public use without any authentication required. Requests originating from the same IP address is limited to a maximum of 5000 requests per day.

Available endpoints:
List snapshots
List nodes
Node status
Node latency
Node Bitcoin address
Leaderboard
Node ranking
List data propagation
Data propagation



DNS seeder
New snapshot is generally available approximately every 5 minutes, so it is recommended that periodical requests are made at most every 5 minutes. Timestamps used in requests and responses are in Unix time in seconds unless specified otherwise in the documentation below.

LIST SNAPSHOTS
List all snapshots that are available on the server from the latest to oldest snapshot. Snapshots are currently kept on the server for up to 60 days.

GET https://bitnodes.io/api/v1/snapshots/
Parameters:

page (optional) - Current page (1).
limit (optional) - Number of snapshots to return (10). Max. 100.
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/snapshots/
{
    "count": 51583,
    "next": "https://bitnodes.io/api/v1/snapshots/?page=2",
    "previous": null,
    "results": [
        {
            "url": "https://bitnodes.io/api/v1/snapshots/1489993827/",
            "timestamp": 1489993827,
            "total_nodes": 6498,
            "latest_height": 458082
        },
        .
        .
        {
            "url": "https://bitnodes.io/api/v1/snapshots/1489990915/",
            "timestamp": 1489990915,
            "total_nodes": 6473,
            "latest_height": 458074
        }
    ]
}


LIST NODES
List all reachable nodes from a snapshot.

GET https://bitnodes.io/api/v1/snapshots/<TIMESTAMP>/
<TIMESTAMP> can be replaced with "latest" to get the list of all reachable nodes from the latest snapshot.

Parameters:

field (optional) - Specify field=coordinates to get the list of unique latitude and longitude pairs or field=user_agents to get the list of unique user agents instead of the full information listed below.
Pagination parameters are not available for this endpoint, as it simply returns the raw snapshot from the export directory used by the crawler. Values in each array in the response represent the following information:

Protocol version
User agent
Connected since
Services
Height
Hostname
City
Country code
Latitude
Longitude
Timezone
ASN
Organization name
Examples:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/snapshots/1489993827/
{
    "timestamp": 1489993827,
    "total_nodes": 6498,
    "latest_height": 334083,
    "nodes": {
        "88.99.167.175:8333": [
            70015,
            "/Satoshi:0.14.0/",
            1489807093,
            13,
            458082,
            "dazzlepod.com",
            null,
            "DE",
            51.2993,
            9.491,
            "Europe/Berlin",
            "AS24940",
            "Hetzner Online GmbH"
        ],
        .
        .
        "208.118.235.190:8333": [
            70015,
            "/Satoshi:0.14.0/",
            1489807094,
            13,
            458082,
            "bitcoin1p.fsf.org",
            "Boston",
            "US",
            42.3584,
            -71.0598,
            "America/New_York",
            "AS22989",
            "Free Software Foundation, Inc."
        ]
    }
}

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/snapshots/latest/?field=coordinates
{
    "timestamp": 1489994806,
    "total_nodes": 6508,
    "latest_height": 458082,
    "coordinates": [
        [
            55.0411,
            82.9344
        ],
        .
        .
        [
            64.8985,
            25.5735
        ]
    ]
}

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/snapshots/latest/?field=user_agents
{
    "timestamp": 1493786400,
    "total_nodes": 6773,
    "latest_height": 464603,
    "user_agents": [
        "/BitcoinUnlimited:1.0.1.1(EB0; AD12)/",
        .
        .
        "/Classic:1.2.0(EB3.7)/"
    ]
}


NODE STATUS
Get status for an activated node. New node must be activated separately, i.e. from https://bitnodes.io/nodes/<ADDRESS>-<PORT>/, before it can be accessed from this endpoint.

GET https://bitnodes.io/api/v1/nodes/<ADDRESS>-<PORT>/
Status string in the response indicates the status of this node as of the last snapshot. Possible values for status are:

PENDING - The node has just been activated pending availability of a new snapshot.
UP - The node is currently reachable.
DOWN - The node is currently unreachable.
Values in the data array represent the following information:

Protocol version
User agent
Connected since
Services
Height
Hostname
City
Country code
Latitude
Longitude
Timezone
ASN
Organization name
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/nodes/208.118.235.190-8333/
{
    "hostname": "",
    "address": "208.118.235.190",
    "status": "UP",
    "data": [
        70015,
        "/Satoshi:0.14.0/",
        1489807094,
        13,
        458082,
        "bitcoin1p.fsf.org",
        "Boston",
        "US",
        42.3584,
        -71.0598,
        "America/New_York",
        "AS22989",
        "Free Software Foundation, Inc."
    ],
    "bitcoin_address": "",
    "url": "",
    "verified": false,
    "mbps": "1.402840"
}


NODE LATENCY
Get daily, weekly and monthly latency data for an activated node. New node must be activated separately, i.e. from https://bitnodes.io/nodes/<ADDRESS>-<PORT>/, before it can be accessed from this endpoint.

GET https://bitnodes.io/api/v1/nodes/<ADDRESS>-<PORT>/latency/
The daily_latency array contains the daily average latency for this node formatted as a time series data points with 15-minute interval. The weekly_latency array contains the weekly average latency for this node formatted as a time series data points with 1-hour interval. The monthly_latency array contains the monthly average latency for this node formatted as a time series data points with 1-day interval. Each data point has the following keys:

t - Timestamp of this data point.
v - Average latency of this node in milliseconds; v = -1 (node is unreachable), v = 0 (node is reachable but no latency data is available).
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/nodes/208.118.235.190-8333/latency/
{
    "daily_latency": [
        {
            "t": 1489907700,
            "v": 111
        },
        .
        .
        {
            "t": 1489994100,
            "v": 111
        }
    ],
    "weekly_latency": [
        {
            "t": 1489388400,
            "v": 111
        },
        .
        .
        {
            "t": 1489993200,
            "v": 111
        }
    ],
    "monthly_latency": [
        {
            "t": 1483228800,
            "v": 0
        },
        .
        .
        {
            "t": 1489968000,
            "v": 110
        }
    ]
}


NODE BITCOIN ADDRESS
Set Bitcoin address for an activated node. New node must be activated separately, i.e. from https://bitnodes.io/nodes/<ADDRESS>-<PORT>/, before it can be updated using this endpoint.

POST https://bitnodes.io/api/v1/nodes/<ADDRESS>-<PORT>/
Parameters:

bitcoin_address - Bitcoin address for this node. An empty value is allowed to remove the existing Bitcoin address.
url (optional) - URL for a static public web page containing the same Bitcoin address. URL must match either http://<ADDRESS>:<PORT> or https://<ADDRESS>:<PORT>. <PORT> is optional and must be below 1024, i.e. privileged port. IPv6 URL is distinguished by enclosing the ADDRESS literal within square brackets, e.g. http://[<ADDRESS>]:<PORT>.
This endpoint must be requested from a client with the same IP address as the node that is being updated. It may take a few minutes before the Bitcoin address is verified separately. The verification process requires the node to have a static public web page at http://<ADDRESS> or http://[<ADDRESS>] for IPv6 URL (if url parameter is not specified) that contains the same Bitcoin address. Verified Bitcoin address will be kept for 30 days before it is verified again using the same verification process.

Examples:

$ curl -H "Accept: application/json; indent=4" -d "bitcoin_address=3JqGyB5AWySQNU7ESHMezadYsR5B8zjSQH" -d "url=http://88.99.167.175" https://bitnodes.io/api/v1/nodes/88.99.167.175-8333/
{
    "success": true
}

$ curl -H "Accept: application/json; indent=4" -d "bitcoin_address=3JqGyB5AWySQNU7ESHMezadYsR5B8zjSQH" -d "url=http://[2a01:4f8:212:3b1f::2]" https://bitnodes.io/api/v1/nodes/2a01:4f8:212:3b1f::2-8333/
{
    "success": true
}


LEADERBOARD
List all activated nodes according to their Peer Index (PIX) in descending order.

GET https://bitnodes.io/api/v1/nodes/leaderboard/
Parameters:

page (optional) - Current page (1).
limit (optional) - Number of nodes to return (10). Max. 100.
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/nodes/leaderboard/
{
    "count": 10754,
    "next": "https://bitnodes.io/api/v1/nodes/leaderboard/?page=2",
    "previous": null,
    "results": [
        {
            "node": "31.132.136.35:8333",
            "vi": "1.0000",
            "si": "1.0000",
            "hi": "1.0000",
            "ai": "1.0000",
            "pi": "1.0000",
            "dli": "1.0000",
            "dui": "0.9897",
            "wli": "1.0000",
            "wui": "1.0000",
            "mli": "1.0000",
            "mui": "0.9839",
            "nsi": "0.9000",
            "ni": "0.0129",
            "bi": "1.0000",
            "peer_index": "9.2046",
            "rank": 1
        },
        .
        .
        {
            "node": "62.43.198.56:8333",
            "vi": "1.0000",
            "si": "1.0000",
            "hi": "1.0000",
            "ai": "0.8477",
            "pi": "1.0000",
            "dli": "1.0000",
            "dui": "1.0000",
            "wli": "1.0000",
            "wui": "1.0000",
            "mli": "1.0000",
            "mui": "0.9818",
            "nsi": "0.9000",
            "ni": "0.0114",
            "bi": "1.0000",
            "peer_index": "9.1007",
            "rank": 9
        }
    ]
}


NODE RANKING
Get ranking and associated Peer Index (PIX) data for an activated node. New node must be activated separately, i.e. from https://bitnodes.io/nodes/<ADDRESS>-<PORT>/, before it can be accessed from this endpoint.

GET https://bitnodes.io/api/v1/nodes/leaderboard/<ADDRESS>-<PORT>/
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/nodes/leaderboard/31.132.136.35-8333/
{
    "node": "31.132.136.35:8333",
    "vi": "1.0000",
    "si": "1.0000",
    "hi": "1.0000",
    "ai": "1.0000",
    "pi": "1.0000",
    "dli": "1.0000",
    "dui": "0.9897",
    "wli": "1.0000",
    "wui": "1.0000",
    "mli": "1.0000",
    "mui": "0.9839",
    "nsi": "0.9000",
    "ni": "0.0129",
    "bi": "1.0000",
    "peer_index": "9.2046",
    "rank": 1
}


LIST DATA PROPAGATION
List up to 100,000 recent invs with data propagation available through data propagation endpoint.

GET https://bitnodes.io/api/v1/inv/
Parameters:

page (optional) - Current page (1).
limit (optional) - Number of invs to return (100). Max. 1000.
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/inv/
{
    "count": 100000,
    "next": "https://bitnodes.io/api/v1/inv/?page=2",
    "previous": null,
    "results": [
        {
            "inv_hash": "8fd8204bd0871c5803154d137a7c3d9f591ed44c8758e3a7e9b27eebb5aeb8e3"
        },
        {
            "inv_hash": "ee1edc3c48b4961bdbf7932998fe2ecab7fec145d209a64f68cef498392a5e1b"
        },
        .
        .
        {
            "inv_hash": "7c673999fe04b2f74e69865e507d8436ac93d35d72051c0bb203e336af281ef2"
        }
    ]
}


DATA PROPAGATION
Get inv propagation stats in milliseconds for a block or transaction broadcasted over 8 hours ago. Stats are calculated based on the inv arrival times (UNIX time in milliseconds) from the first 1000 nodes.

GET https://bitnodes.io/api/v1/inv/<INV_HASH>/
Values in stats represent the following information:

head - Arrival times for the first 10 nodes in a list of ["<ADDRESS>:<PORT>", <TIMESTAMP>].
min - Delta for earliest arrival time. Value can be 0 if the delta is less than 1 millisecond.
max - Delta for latest arrival time.
mean - Average of deltas.
std - Standard deviation of deltas.
50% - 50th percentile of deltas.
90% - 90th percentile of deltas.
Example:

$ curl -H "Accept: application/json; indent=4" https://bitnodes.io/api/v1/inv/1e0942ab85c1800b3cfa3997af73803ee0cda22b657fd307285ee8f08045654a/
{
    "inv_hash": "1e0942ab85c1800b3cfa3997af73803ee0cda22b657fd307285ee8f08045654a",
    "stats": {
        "head": [
            [
                "37.59.121.19:8333",
                1469655511942
            ],
            [
                "[2a01:4f8:c17:392a::2]:8333",
                1469655511942
            ],
            [
                "82.25.241.194:8333",
                1469655511943
            ],
            [
                "188.222.127.147:8333",
                1469655511943
            ],
            [
                "52.21.250.237:8333",
                1469655511943
            ],
            [
                "94.23.15.33:8333",
                1469655511943
            ],
            [
                "91.125.151.190:8333",
                1469655511943
            ],
            [
                "216.59.4.212:8333",
                1469655511944
            ],
            [
                "185.117.75.141:8333",
                1469655511944
            ],
            [
                "178.85.167.122:8333",
                1469655511944
            ]
        ],
        "min": 0,
        "max": 518,
        "mean": 208,
        "std": 143,
        "50%": 193,
        "90%": 433
    }
}


DNS SEEDER
Get a list of reachable nodes to bootstrap your Bitcoin client connection to the Bitcoin network.

DNS <RECORD> <PREFIX>seed.bitnodes.io
IPv4 nodes are returned via A records, IPv6 nodes are returned via AAAA records and .onion nodes are returned via TXT records.

seed.bitnodes.io returns a list of reachable nodes with NODE_NETWORK (1) service bit set. Prefix x[1-f] is accepted to filter nodes by specific services. For example, x5.seed.bitnodes.io returns a list of reachable nodes with services set to 5, i.e. NODE_NETWORK (1), NODE_BLOOM (4).

Example:

$ dig A seed.bitnodes.io
seed.bitnodes.io.	60	IN	A	31.170.106.203
seed.bitnodes.io.	60	IN	A	51.175.33.95
seed.bitnodes.io.	60	IN	A	59.153.151.157
seed.bitnodes.io.	60	IN	A	62.43.130.178
seed.bitnodes.io.	60	IN	A	62.45.122.228
seed.bitnodes.io.	60	IN	A	70.48.106.185
seed.bitnodes.io.	60	IN	A	72.36.89.11
seed.bitnodes.io.	60	IN	A	78.94.22.14
seed.bitnodes.io.	60	IN	A	82.134.66.146
seed.bitnodes.io.	60	IN	A	85.194.238.3
seed.bitnodes.io.	60	IN	A	89.34.237.187
seed.bitnodes.io.	60	IN	A	91.188.125.252
seed.bitnodes.io.	60	IN	A	93.115.86.246
seed.bitnodes.io.	60	IN	A	94.26.41.139
seed.bitnodes.io.	60	IN	A	94.181.44.104
seed.bitnodes.io.	60	IN	A	95.79.35.50
seed.bitnodes.io.	60	IN	A	95.87.226.145
seed.bitnodes.io.	60	IN	A	108.16.110.245
seed.bitnodes.io.	60	IN	A	156.34.169.15
seed.bitnodes.io.	60	IN	A	176.10.116.242
seed.bitnodes.io.	60	IN	A	192.124.224.7
seed.bitnodes.io.	60	IN	A	192.243.215.14
seed.bitnodes.io.	60	IN	A	217.61.216.12
seed.bitnodes.io.	60	IN	A	219.102.94.151
seed.bitnodes.io.	60	IN	A	222.166.170.51

$ dig AAAA seed.bitnodes.io
seed.bitnodes.io.	60	IN	AAAA	2001:470:1f07:151c:baac:6fff:feb7:3ba9
seed.bitnodes.io.	60	IN	AAAA	2001:67c:440:831::254
seed.bitnodes.io.	60	IN	AAAA	2001:4801:7819:74:b745:b9d5:ff10:aaec
seed.bitnodes.io.	60	IN	AAAA	2602:ffc5:20::1:eda5
seed.bitnodes.io.	60	IN	AAAA	2602:ffda:ccc:1::b3fa:73b2
seed.bitnodes.io.	60	IN	AAAA	2607:5300:60:3ddf::
seed.bitnodes.io.	60	IN	AAAA	2803:80:1004:283::1
seed.bitnodes.io.	60	IN	AAAA	2a00:14f0:e000:80d2:cd1a::1
seed.bitnodes.io.	60	IN	AAAA	2a00:6740:1:0:a88e:28e4:128c:8e11
seed.bitnodes.io.	60	IN	AAAA	2a00:7c80:0:71::8
seed.bitnodes.io.	60	IN	AAAA	2a00:8a60:e012:a00::21
seed.bitnodes.io.	60	IN	AAAA	2a01:e35:8aba:8220:225:ff:fe42:bd6b
seed.bitnodes.io.	60	IN	AAAA	2a01:8e01:d949:6ee7:21b:21ff:fe00:1003
seed.bitnodes.io.	60	IN	AAAA	2a01:b000::4166:515b:ef9e:b3
seed.bitnodes.io.	60	IN	AAAA	2a04:3542:1000:910:8492:b8ff:fe91:711d

$ dig A x5.seed.bitnodes.io
x5.seed.bitnodes.io.	60	IN	A	5.43.123.225
x5.seed.bitnodes.io.	60	IN	A	31.10.91.118
x5.seed.bitnodes.io.	60	IN	A	31.164.7.100
x5.seed.bitnodes.io.	60	IN	A	37.205.10.140
x5.seed.bitnodes.io.	60	IN	A	45.72.130.207
x5.seed.bitnodes.io.	60	IN	A	69.41.3.212
x5.seed.bitnodes.io.	60	IN	A	72.18.158.210
x5.seed.bitnodes.io.	60	IN	A	79.172.194.219
x5.seed.bitnodes.io.	60	IN	A	82.220.91.142
x5.seed.bitnodes.io.	60	IN	A	85.214.73.33
x5.seed.bitnodes.io.	60	IN	A	89.22.151.211
x5.seed.bitnodes.io.	60	IN	A	89.179.247.236
x5.seed.bitnodes.io.	60	IN	A	94.26.41.139
x5.seed.bitnodes.io.	60	IN	A	118.192.48.46
x5.seed.bitnodes.io.	60	IN	A	123.3.221.32
x5.seed.bitnodes.io.	60	IN	A	162.157.190.145
x5.seed.bitnodes.io.	60	IN	A	162.247.50.107
x5.seed.bitnodes.io.	60	IN	A	171.249.169.43
x5.seed.bitnodes.io.	60	IN	A	176.24.51.227
x5.seed.bitnodes.io.	60	IN	A	180.151.85.195
x5.seed.bitnodes.io.	60	IN	A	193.105.125.173
x5.seed.bitnodes.io.	60	IN	A	193.239.80.155
x5.seed.bitnodes.io.	60	IN	A	199.101.100.59
x5.seed.bitnodes.io.	60	IN	A	213.5.36.58
x5.seed.bitnodes.io.	60	IN	A	213.92.236.92

$ dig TXT x5.seed.bitnodes.io
x5.seed.bitnodes.io.	60	IN	TXT	"5cg7qeywvwo6vxpt.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"6tgbzyioekux5nkx.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"bitcoingxe5ufsb7.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"boa3km6cince3nqo.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"bs4bq6s6qkvt5hpi.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"cernrmrk5zomzozn.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"eerip5sn7pgeuhnd.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"fnerivxiha7z2spn.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"kxqrku2hk2n73afa.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"m7hrecvhdodr4v2x.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"mwu5og2agcspmgkx.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"n5fp6kwn7xpjiwwz.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"okdzjarwekbshnof.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"oyebydl2pacx6v26.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"p5mx2imj75dpmime.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"pcfhsdqzs6q63ryu.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"rlafimkctvz63llg.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"rpi66o4epj66ldrk.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"rxjvy5eyttep5tts.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"tuiyvqgi3o675pjb.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"tx4zd7d5exonnblh.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"visevrizz3quyagj.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"wfsx2gi7djhy22hk.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"ycivnom44dmxx4ob.onion"
x5.seed.bitnodes.io.	60	IN	TXT	"z4fax2vxg23t2ddf.onion"




JOIN THE NETWORK
Be part of the Bitcoin network by running a Bitcoin full node, e.g. Bitcoin Core.



 ©️ 2021 ADDY YEOW (PGP)
Support Bitnodes (3JqGyB5AWySQNU7ESHMezadYsR5B8zjSQH)

ABOUT BITNODES
Bitnodes is currently being developed to estimate the size of the Bitcoin network by finding all the reachable nodes in the network. The current methodology involves sending getaddr messages recursively to find all the reachable nodes in the network, starting from a set of seed nodes. Bitnodes uses Bitcoin protocol version 70001 (i.e. >= /Satoshi:0.8.x/), so nodes running an older protocol version will be skipped. The crawler implementation in Python is available from GitHub (ayeowch/bitnodes) and the crawler deployment is documented in Provisioning Bitcoin Network Crawler.

The crawler maintained by Bitnodes connects from these IP addresses: 88.99.167.175, 88.99.167.186, 2a01:4f8:10a:37ee::2
